## 🔧 Fase 2 – Configuración de router virtual y conexión entre subredes

**🌟 Objetivo:**
Conectar la red interna a la red externa mediante un router virtual, permitiendo a las instancias acceder a Internet o ser accedidas desde fuera mediante Floating IP.

**📜 Descripción:**
En esta fase se creará un router que actúe como gateway para las subredes internas. Se configurará su interfaz interna y su puerta de enlace externa. Esto habilita el tráfico norte-sur controlado.

---

### 🧭 Acciones desde CLI

1. Crear un router:

   ```bash
   openstack router create router-interno
   ```

2. Conectar el router a la subred interna:

   ```bash
   openstack router add subnet router-interno subred-interna
   ```

3. Establecer la red externa como puerta de enlace:

   ```bash
   openstack router set --external-gateway red-externa router-interno
   ```

4. Verificar configuración:

   ```bash
   openstack router show router-interno
   ```

---

### 💥 Acciones desde Horizon

* Ir a **Project > Network > Routers**
* Crear un nuevo router y seleccionar la red externa como gateway
* Editar el router y añadir la subred interna como interfaz
* Revisar la topología para confirmar conexión visual entre redes

---

### ⚖️ Comparativa CLI vs Horizon

| Acción                   | CLI (openstack)         | Horizon (web UI)           |
| ------------------------ | ----------------------- | -------------------------- |
| Crear router             | ✅ Rápido y scriptable   | ⚠️ Requiere varios pasos   |
| Añadir interfaz a subred | ✅ Paso explícito        | ⚠️ Puede quedar olvidado   |
| Visualizar topología     | ❌                       | ✅ Muy útil para validación |
| Trazabilidad de errores  | ✅ Logs y outputs claros | ⚠️ Mensajes menos precisos |

---

### 🧹 Retos para el alumno

1. Crear un segundo router y conectar otra subred interna (por ejemplo, `red-interna-b`)
2. Intentar borrar un router en uso: ¿qué lo impide?
3. Comprobar si se puede crear un router sin gateway externo y aún así conectar subredes

---

### ✅ Validaciones esperadas

* El router aparece en `openstack router list` y está activo
* Tiene al menos una interfaz interna conectada a una subred
* Su gateway externo está configurado correctamente hacia `red-externa`
* Desde una instancia en `red-interna`, se puede hacer ping a `8.8.8.8` si se añade Floating IP (según reglas)

---

## 🤩 Reto final (funcional): Habilitar acceso a Internet solo desde dentro

**Escenario:**
Tienes una instancia CirrOS conectada a la red interna. Quieres que **pueda hacer ping a Internet**, pero **no sea accesible desde fuera** (sin Floating IP asignada).

**Objetivo:**
➡️ Lograr que la instancia tenga salida a Internet (SNAT) pero no entrada desde el exterior.

### 📌 Pistas

* Comprueba si el router con gateway externo aplica SNAT por defecto.
* Revisa si la instancia necesita Floating IP para salida.
* Usa `ping 8.8.8.8` desde CirrOS y observa si hay respuesta.
* Asegúrate de que **no** has asignado Floating IP.
* Verifica que desde el host externo **no puedes hacer `ping` a la instancia**.
