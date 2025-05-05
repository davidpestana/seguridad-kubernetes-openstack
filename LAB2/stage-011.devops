## 🔁 Fase 10.bis – Automatización parcial con Ansible sobre OpenStack

**🌟 Objetivo:**
Automatizar desde Ansible el ciclo de creación de una instancia CirrOS y su configuración como servidor simple mediante `nc`, separando la lógica en playbooks independientes. Se requiere intervención mínima del alumno para completar el flujo (inventario manual).

**📜 Descripción:**
En esta fase se estructura el flujo de trabajo en dos playbooks y varios roles. Primero, se genera una clave SSH (si no existe), se registra en OpenStack y se lanza la instancia CirrOS. Después, el alumno añade la IP al inventario. El segundo playbook configura el servicio `nc` y valida su exposición.

---

## 📂 Estructura de proyecto sugerida

```bash
inventories/
  hosts.ini
roles/
  generate_ssh_key/
  create_cirros_instance/
  configure_nc/
  validate_access/
playbooks/
  create-node.yml
  run-services.yml
```

---

## ▶️ Playbook 1: `create-node.yml`

```yaml
- name: Crear instancia CirrOS desde OpenStack
  hosts: localhost
  gather_facts: false
  roles:
    - role: generate_ssh_key
    - role: create_cirros_instance
```

Este playbook:

* Genera un par de claves SSH si no existen (`generate_ssh_key`)
* Registra la clave en OpenStack
* Crea la instancia `cirros-ansible` y muestra la IP al finalizar

### 📝 Acción requerida

El alumno debe añadir la IP obtenida manualmente al inventario `hosts.ini`:

```ini
[cirros]
10.100.0.17 ansible_user=cirros ansible_ssh_private_key_file=~/.ssh/id_rsa_cirros
```

---

## ▶️ Playbook 2: `run-services.yml`

```yaml
- name: Configurar servicios y validar acceso
  hosts: cirros
  gather_facts: false
  roles:
    - role: configure_nc
    - role: validate_access
```

Este playbook:

* Instala `nc` en la instancia CirrOS (si es necesario)
* Levanta un servicio en el puerto 80
* Espera desde `tower` que el puerto esté disponible

---

### 💥 Ejemplo de rol `configure_nc`

```yaml
- name: Instalar netcat (si falta)
  raw: apk add busybox-extras || true

- name: Lanzar servidor HTTP simple
  shell: "nohup nc -lk -p 80 -e /bin/echo &"
```

### 💥 Ejemplo de rol `validate_access`

```yaml
- name: Esperar a que el servicio esté accesible
  delegate_to: localhost
  wait_for:
    host: "{{ ansible_host }}"
    port: 80
    timeout: 10
```

---

### 🧹 Retos para el alumno

1. Añadir correctamente la IP al inventario `[cirros]`
2. Lanzar ambos playbooks en orden
3. Cambiar el puerto del servicio a 8080 y validar
4. Replicar el flujo completo para una segunda instancia

---

### ✅ Validaciones esperadas

* El primer playbook despliega correctamente la instancia
* La IP se encuentra operativa vía SSH desde `tower`
* El segundo playbook instala y lanza `nc` en el puerto 80
* La validación de conectividad confirma que el servicio responde

---

## 🤩 Reto final (funcional): Automatizar creación, configuración y test de servicio

**Escenario:**
Necesitas preparar una instancia CirrOS accesible por red que escuche en un puerto determinado y devuelva respuesta inmediata. Todo debe estar automatizado salvo la inclusión de IP al inventario.

**Objetivo:**
➡️ Ejecutar el flujo completo en dos pasos y validar su funcionamiento desde el nodo `tower`.

### 📌 Pistas

* Usa claves únicas por ejecución si quieres mantener múltiples VMs
* Puedes mostrar la IP con `debug` al final del primer playbook
* Valida también conectividad desde otra instancia si está disponible
