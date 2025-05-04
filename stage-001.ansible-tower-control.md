## Stage 001 - Preparación de la máquina "tower" (Ansible Control Node)

### 📚 Objetivos de la fase

Dejar preparada la máquina `tower` para funcionar como nodo de control de Ansible con:

* Ansible instalado y funcional
* Acceso SSH a las VMs `openstack` y `k8s`
* Estructura inicial para trabajar con inventarios, playbooks y roles

---

### 🔧 Pasos a realizar

1. **Conectarse a la VM**

```bash
vagrant ssh tower
```

2. **Actualizar el sistema e instalar dependencias**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y python3-pip git curl vim unzip
```


Durante la actualización del sistema, es posible que aparezca una pantalla interactiva preguntando qué servicios deben reiniciarse (por ejemplo: dbus.service, networkd-dispatcher.service).
Recomendación: no seleccionar ninguno y confirmar con Ok, para evitar interrupciones en la conectividad o la sesión SSH.

3. **Instalar Ansible**

```bash
pip3 install --user ansible

# Añadir binarios locales al PATH
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Validar versión
ansible --version
```

4. **Crear estructura de trabajo**

```bash
mkdir -p ~/labs/{inventory,playbooks,roles}
```

5. **Obtener IPs privadas del resto de VMs**

Desde `openstack` y `k8s`:

```bash
ip a
```

6. **Probar conectividad desde `tower`**

```bash
ping -c 3 <ip_de_openstack>
ping -c 3 <ip_de_k8s>
```

7. **Probar acceso SSH desde `tower` sin password**

```bash
ssh vagrant@<ip_de_openstack>
ssh vagrant@<ip_de_k8s>
```

> Si pide confirmación de clave, aceptar. No debería pedir contraseña.

---

### ✅ Validaciones necesarias

* [ ] El comando `ansible --version` devuelve correctamente
* [ ] Se puede hacer ping a `openstack` y `k8s` desde `tower`
* [ ] El usuario `vagrant` puede acceder por SSH sin contraseña a las otras VMs
* [ ] Existen las carpetas `~/labs/inventory`, `~/labs/playbooks`, `~/labs/roles`

---

### 📂 Estructura de archivos esperada

```
~/labs/
├── inventory/
├── playbooks/
└── roles/
```

---

### 🔗 Documentación oficial relacionada

* [https://docs.ansible.com/](https://docs.ansible.com/)
* [https://developer.hashicorp.com/vagrant/docs](https://developer.hashicorp.com/vagrant/docs)
* [https://docs.openstack.org/devstack/latest/](https://docs.openstack.org/devstack/latest/)
