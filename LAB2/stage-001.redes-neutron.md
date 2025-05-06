## 🔧 Fase 1 – Creación de redes internas y externas en Neutron

**🌟 Objetivo:**
Definir las redes internas y externas que usará OpenStack para segmentar el tráfico de instancias y permitir accesos desde el exterior de forma controlada.

**📜 Descripción:**
En esta fase se construirán los elementos básicos de red necesarios para los ejercicios posteriores:

* Una **red interna**, aislada y destinada al tráfico entre instancias del mismo proyecto.
* Una **red externa**, que permitirá asociar Floating IPs a instancias seleccionadas para exponer servicios.

Se mostrará cómo hacerlo tanto desde la CLI como desde Horizon, resaltando similitudes y diferencias.

---

### 🧭 Acciones desde CLI

1. Crear red interna y su subred:

   ```bash
   openstack network create red-interna
   openstack subnet create --network red-interna --subnet-range 10.0.10.0/24 subred-interna
   ```

2. Crear red externa (modo flat):

   ```bash
   openstack network create red-externa --external --provider-network-type flat --provider-physical-network physnet1
   openstack subnet create --network red-externa --subnet-range 203.0.113.0/24 \
     --no-dhcp --gateway 203.0.113.1 \
     --allocation-pool start=203.0.113.100,end=203.0.113.200 subred-externa
   ```

---

### 💥 Acciones desde Horizon

* Accede a **Project > Network > Networks**
* Crea una red interna: marca “Create Subnet” y define un rango IP
* Crea una red externa: marca “External Network” y desactiva DHCP
* Asegúrate de que la red externa está marcada como compartida y tiene rango válido

---

### ⚖️ Comparativa CLI vs Horizon

| Acción                 | CLI (openstack)              | Horizon (web UI)              |
| ---------------------- | ---------------------------- | ----------------------------- |
| Creación rápida        | ✅ (scripts o copia/pega)     | ❌ (interfaz más lenta)        |
| Detalle y control      | ✅ (todos los flags visibles) | ⚠️ (algunos detalles ocultos) |
| Visualización de redes | ❌                            | ✅ (topología visual)          |
| Automatización posible | ✅                            | ❌                             |

---

### 🧹 Retos para el alumno

1. Crear una segunda red interna llamada `red-interna-b` con un rango distinto (por ejemplo `10.0.20.0/24`)
2. Comprobar si se puede marcar una red como compartida desde Horizon
3. Borrar una red recién creada: ¿cuándo te deja y cuándo no?

---

### ✅ Validaciones esperadas

* Ambas redes deben aparecer en `openstack network list` y en Horizon
* La red externa debe mostrar `router:external = True`
* La subred externa debe tener `DHCP desactivado` y `gateway` definido
* La red interna debe permitir asociar instancias en la próxima fase

---

## 🤩 Reto final (funcional): Publicar un servicio solo accesible por el puerto 80

**Escenario:**
Has lanzado una instancia CirrOS en la red interna y deseas exponer un servicio en el puerto 80 al exterior (simulado con `nc`). El puerto 22 (simulando SSH) **no debe ser accesible desde fuera**.

**Objetivo:**
➡️ Lograr que **solo el puerto 80** esté accesible públicamente mediante Floating IP.

### 📌 Pistas (sin solución directa)

* Usa `nc` dentro de la instancia para simular un servicio HTTP y otro en el puerto 22.
* Verifica qué reglas existen en tu Security Group por defecto.
* Intenta desde fuera un `nc -vz` a ambos puertos.
* Ajusta tu configuración hasta que **solo el 80 responda**.
* Usa Horizon o CLI para comprobar y editar reglas.
