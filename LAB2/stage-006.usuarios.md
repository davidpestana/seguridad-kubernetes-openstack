## 🔧 Fase 6 – Creación de usuarios y proyectos en Keystone

**🌟 Objetivo:**
Gestionar la identidad en OpenStack mediante Keystone: crear usuarios, proyectos (tenants), y preparar la base para aplicar control de acceso segmentado.

**📜 Descripción:**
En esta fase trabajaremos con la capa de autenticación y autorización de OpenStack: **Keystone**. Se crearán nuevos proyectos y usuarios, se vincularán mediante roles, y se explorará cómo el aislamiento por proyecto afecta a la visibilidad de recursos de red y computación.

---

### 🧭 Acciones desde CLI

1. **Crear un nuevo proyecto:**

   ```bash
   openstack project create proyecto-demo --description "Proyecto de prueba"
   ```

2. **Crear un nuevo usuario:**

   ```bash
   openstack user create --password demo123 --project proyecto-demo usuario-demo
   ```

3. **Asignar un rol (por ejemplo, miembro):**

   ```bash
   openstack role add --project proyecto-demo --user usuario-demo member
   ```

4. **Verificar asignaciones:**

   ```bash
   openstack role assignment list --user usuario-demo --project proyecto-demo
   ```

5. **Listar proyectos y usuarios:**

   ```bash
   openstack project list
   openstack user list
   ```

---

### 💥 Acciones desde Horizon

* Ir a **Identity > Projects** y crear uno nuevo
* Ir a **Identity > Users** y crear uno nuevo, asociándolo al proyecto
* Ir a **Identity > Roles** y desde ahí asignar al usuario su rol en el proyecto

---

### ⚖️ Comparativa CLI vs Horizon

| Acción         | CLI                | Horizon                      |
| -------------- | ------------------ | ---------------------------- |
| Crear proyecto | ✅ Rápido y directo | ✅ Visual e intuitivo         |
| Crear usuario  | ✅ Sin formulario   | ✅ Con gestión de contraseñas |
| Asignar rol    | ✅ Preciso          | ⚠️ Varios clics necesarios   |

---

### 🧹 Retos para el alumno

1. Crear un segundo usuario sin asignarlo a ningún proyecto: ¿puede autenticar?
2. Crear un nuevo proyecto y comprobar qué recursos ve desde Horizon (sin redes ni instancias)
3. Verificar si un usuario puede crear instancias en un proyecto ajeno

---

### ✅ Validaciones esperadas

* El usuario puede autenticarse con `--os-username` y `--os-project-name`
* Solo tiene visibilidad sobre los recursos de su proyecto
* No puede listar ni modificar recursos de otros proyectos

---

## 🤩 Reto final (funcional): Crear un entorno multiusuario con visibilidad aislada

**Escenario:**
Tienes dos usuarios en dos proyectos distintos (`devops-a` y `devops-b`). Deseas garantizar que **cada uno vea solo sus instancias, redes y Floating IPs**, sin interferencia entre ambos.

**Objetivo:**
➡️ Validar que el aislamiento de recursos por proyecto se aplica correctamente usando Keystone.

### 📌 Pistas

* Usa `openstack server list` tras autenticarte con las credenciales de cada usuario
* Intenta crear una red en un proyecto y acceder desde el otro
* Compara las vistas en Horizon para `devops-a` vs `devops-b`
