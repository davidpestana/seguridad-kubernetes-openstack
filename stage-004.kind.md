## Stage 004 - Desplegar laboratorio de KIND accesible desde el host

### 📚 Objetivos de la fase

Automatizar el despliegue de un clúster Kubernetes con KIND compuesto por 1 nodo de control y 2 workers sobre una VM Ubuntu gestionada por Vagrant. El entorno debe quedar accesible desde el host mediante el puerto 6443, y el `kubeconfig` debe ser parcheado para apuntar a la IP pública del host. El objetivo es facilitar el uso de herramientas como `kubectl` o Lens desde el exterior.

---

### 🔧 Pasos realizados

1. **Diseñar playbook y rol para KIND**

Se crean las siguientes tareas en `roles/kind/tasks/main.yml`:

```yaml
- name: Instalar dependencias del sistema
  apt:
    name:
      - apt-transport-https
      - ca-certificates
      - curl
      - software-properties-common
    state: present
    update_cache: true

- name: Añadir clave GPG de Docker
  shell: curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
  args:
    creates: /usr/share/keyrings/docker-archive-keyring.gpg

- name: Añadir repositorio de Docker
  apt_repository:
    repo: "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable"
    state: present
    filename: docker

- name: Instalar Docker
  apt:
    name:
      - docker-ce
      - docker-ce-cli
      - containerd.io
    state: present
    update_cache: true

- name: Asegurar que Docker está corriendo
  service:
    name: docker
    state: started
    enabled: true

- name: Añadir usuario vagrant al grupo docker
  user:
    name: vagrant
    groups: docker
    append: yes

- name: Descargar binario kind
  get_url:
    url: https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
    dest: /usr/local/bin/kind
    mode: '0755'

- name: Descargar binario kubectl
  get_url:
    url: https://dl.k8s.io/release/v1.28.0/bin/linux/amd64/kubectl
    dest: /usr/local/bin/kubectl
    mode: '0755'

- name: Crear configuración kind para 3 nodos
  template:
    src: kind-config.yaml.j2
    dest: /home/vagrant/kind-config.yaml
    owner: vagrant
    group: vagrant
    mode: '0644'

- name: Crear clúster kind
  become: false
  become_user: vagrant
  shell: kind create cluster --name {{ kind_cluster_name }} --config /home/vagrant/kind-config.yaml
  environment:
    KUBECONFIG: /home/vagrant/.kube/config
  args:
    creates: "/home/vagrant/.kube/config"

- name: Modificar kubeconfig para exponer API a la IP pública
  become: false
  become_user: vagrant
  lineinfile:
    path: /home/vagrant/.kube/config
    regexp: '^(\s*server:\s*)https://.*:\d+'
    line: '\1https://{{ host_ip }}:6443'
    backrefs: yes

- name: Copiar kubeconfig original al host
  fetch:
    src: /home/vagrant/.kube/config
    dest: "{{ playbook_dir }}/../../.config/kind.kubeconfig"
    flat: true

- name: Parchear IP del servidor en kubeconfig copiado
  delegate_to: localhost
  run_once: true
  lineinfile:
    path: "{{ playbook_dir }}/../../.config/kind.kubeconfig"
    regexp: '^(\s*server:\s*)https://.*:\d+'
    line: '\1https://{{ host_ip }}:6443'
    backrefs: yes
```

---

### ✅ Validaciones necesarias

* [ ] Se puede ejecutar el playbook sin errores:

```bash
ansible-playbook -i inventory.ini playbooks/kubernetes/kind-deploy.yml
```

* [ ] El archivo `labs/.config/kind.kubeconfig` queda generado y contiene la IP del host
* [ ] El clúster responde desde el host:

```bash
export KUBECONFIG=$(pwd)/.config/kind.kubeconfig
kubectl get nodes
```

* [ ] Opcional: funcionamiento en Lens validado con ese `kubeconfig`
* [ ] El clúster contiene 1 nodo de control y 2 workers

```bash
kubectl get nodes -o wide
```

---

### 🗂️ Estructura esperada

```
labs/
├── .config/
│   └── kind.kubeconfig
├── inventory.ini
├── playbooks/
│   └── kubernetes/
│       └── kind-deploy.yml
└── roles/
    └── kind/
        ├── tasks/main.yml
        └── templates/kind-config.yaml.j2
```
