## Stage 003 - Preparar paquete de playbooks y roles básicos para desplegar DevStack

### 📚 Objetivos de la fase

Preparar un paquete de recursos reutilizables (playbooks y roles) que permitan desplegar y validar DevStack desde la máquina de control `tower` sobre la VM `openstack`.

Este paquete se montará como volumen compartido en la VM `tower` para facilitar el desarrollo y ejecución desde VS Code. Se utilizará un inventario centralizado para todos los nodos del laboratorio. El playbook debe dejar DevStack totalmente operativo y accesible desde el host, incluyendo Horizon.

---

### 🔧 Pasos a realizar

> 🔄 Esta sección utiliza un fork personalizado de `requirements` que evita errores con `setup.py` y setuptools. El repositorio se define en el `local.conf` y se elimina la versión oficial antes de ejecutar `stack.sh`.

1. **Crear estructura base del proyecto Ansible**

```bash
mkdir -p ~/labs/playbooks/devstack
mkdir -p ~/labs/roles/devstack/tasks
mkdir -p ~/labs/roles/devstack/files
mkdir -p ~/labs/roles/devstack/templates
```

2. **Inicializar inventario estático unificado para el laboratorio**

```ini
# ~/labs/inventory/hosts.ini
[openstack]
192.168.56.14 ansible_user=vagrant ansible_ssh_private_key_file=~/.ssh/id_rsa

[k8s]
192.168.56.15 ansible_user=vagrant ansible_ssh_private_key_file=~/.ssh/id_rsa

[tower]
127.0.0.1 ansible_connection=local
```

3. **Crear archivo de configuración de Ansible**

```ini
# ~/labs/ansible.cfg
[defaults]
inventory = ./inventory/hosts.ini
host_key_checking = False
retry_files_enabled = False
roles_path = ./roles
```

> 🔔 Para evitar errores como "No inventory was parsed", asegúrese de ejecutar los comandos desde el directorio `~/labs`, donde se encuentra el archivo `ansible.cfg`.

4. **Crear playbook principal para la instalación de DevStack**

```yaml
# ~/labs/playbooks/devstack/deploy.yml
- name: Desplegar DevStack
  hosts: openstack
  become: yes
  pre_tasks:
    - name: Detectar IP de red privada
      set_fact:
        devstack_host_ip: "{{ ansible_all_ipv4_addresses | select('match', '^192\\.168\\.56\\.') | list | first }}"

  roles:
    - devstack
```

5. **Crear archivo de tareas del rol actualizado con uso de fork de requirements**

```yaml
# ~/labs/roles/devstack/tasks/main.yml

- name: Detectar IP de red privada
  set_fact:
    devstack_host_ip: "{{ ansible_all_ipv4_addresses | select('match', '^192\\.168\\.56\\.') | list | first }}"

- name: Instalar dependencias del sistema
  become: true
  apt:
    name:
      - git
      - python3-pip
      - net-tools
      - sudo
    state: present
    update_cache: yes

- name: Clonar DevStack si no existe
  git:
    repo: https://opendev.org/openstack/devstack.git
    dest: /home/vagrant/devstack
    version: master
    update: no
  become: true
  become_user: vagrant

- name: Asegurar que se elimina cualquier local.conf previo
  file:
    path: /home/vagrant/devstack/local.conf
    state: absent
  become: true

- name: Generar archivo local.conf desde plantilla
  template:
    src: local.conf.j2
    dest: /home/vagrant/devstack/local.conf
    mode: '0644'
    force: true
  become: true
  become_user: vagrant

- name: Eliminar /opt/stack/requirements si existe (por si DevStack lo creó en ejecuciones previas)
  file:
    path: /opt/stack/requirements
    state: absent
  become: true

- name: Ejecutar DevStack si no está desplegado
  shell: ./stack.sh > /home/vagrant/stack.log 2>&1
  args:
    chdir: /home/vagrant/devstack
  become: true
  become_user: vagrant
  environment:
    HOME: /home/vagrant
  when: not lookup('file', '/home/vagrant/devstack/.stack_success', errors='ignore')

```

6. **Crear plantilla local.conf.j2**

```jinja2
# ~/labs/roles/devstack/templates/local.conf.j2
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD
HOST_IP={{ devstack_host_ip }}
REQUIREMENTS_REPO=https://github.com/davidpestana/requirements.git
```

7. **Validar que Ansible puede ejecutar el playbook desde tower**

```bash
cd ~/labs
ansible-playbook playbooks/devstack/deploy.yml
```

---

### ✅ Validaciones necesarias

> 🛠️ *Para monitorear la instalación de DevStack mientras se ejecuta `stack.sh`, puede usarse:*
> `ssh vagrant@192.168.56.14 tail -f /home/stack/stack.log`

* [ ] Estructura de carpetas correctamente generada en `~/labs`
* [ ] El archivo `ansible.cfg` existe y apunta al inventario correcto
* [ ] El inventario unificado contiene los grupos esperados
* [ ] Se puede hacer `ansible openstack -m ping` desde `~/labs`
* [ ] El playbook ejecuta todas las tareas sin error
* [ ] DevStack queda clonado con el archivo `local.conf` preparado
* [ ] Se ejecuta `stack.sh` automáticamente desde el rol (solo si no existe `/opt/stack`)
* [ ] Se puede acceder vía `ssh vagrant@192.168.56.14` desde el host
* [ ] Se puede acceder al dashboard de Horizon en `http://192.168.56.14/`
* [ ] El usuario `admin` puede autenticarse con la contraseña definida (`secret`)

---

### 📂 Estructura de archivos esperada

```
~/labs/
├── ansible.cfg
├── inventory/
│   └── hosts.ini
├── playbooks/
│   └── devstack/
│       └── deploy.yml
└── roles/
    └── devstack/
        ├── tasks/
        │   └── main.yml
        └── templates/
            └── local.conf.j2
```

---

### 🔗 Documentación oficial relacionada

* [https://docs.ansible.com/](https://docs.ansible.com/)
* [https://docs.openstack.org/devstack/latest/](https://docs.openstack.org/devstack/latest/)
