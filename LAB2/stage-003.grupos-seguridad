## 🔧 Fase 3 – Aplicación de reglas en Security Groups personalizados

**🌟 Objetivo:**
Definir y aplicar reglas de acceso entrante y saliente mediante Security Groups, controlando el tráfico permitido hacia las instancias.

**📜 Descripción:**
Los Security Groups en OpenStack actúan como firewalls virtuales por instancia, permitiendo o denegando tráfico según puerto, protocolo y dirección. Esta fase cubre la creación, modificación y aplicación de Security Groups personalizados, además de la validación de su efecto práctico usando una instancia CirrOS con servicios simulados por `nc`.

---

### 🧭 Acciones desde CLI

1. **Crear un nuevo Security Group:**

   ```bash
   openstack security group create web-only \
     --description "Permite solo ping y HTTP"
   ```

   > Se crea un grupo vacío sin reglas por defecto (salvo salientes permitidas).

2. **Añadir una regla para ICMP (ping):**

   ```bash
   openstack security group rule create \
     --proto icmp \
     web-only
   ```

   > Esto permite que la instancia responda a `ping` desde el exterior.

3. **Añadir una regla para HTTP (puerto 80 TCP):**

   ```bash
   openstack security group rule create \
     --proto tcp \
     --dst-port 80 \
     web-only
   ```

   > Solo se permitirá tráfico TCP entrante al puerto 80.

4. **Asociar el Security Group a una instancia existente:**

   ```bash
   openstack server add security group cirros-test web-only
   ```

   > Si la instancia ya tiene el grupo `default`, este seguirá activo a menos que lo quites explícitamente.

5. **(Opcional) Verificar las reglas actuales de la instancia:**

   ```bash
   openstack server show cirros-test -f json | jq '.security_groups'
   openstack security group show web-only
   ```

---

### 💥 Acciones desde Horizon

1. Ir a **Project > Network > Security Groups**
2. Crear un nuevo grupo con nombre `web-only` y descripción
3. Ir a la pestaña **Rules** y añadir:

   * ICMP (tipo = -1, código = -1)
   * TCP puerto 80 (puerto inicial y final = 80)
4. Ir a **Instances**, seleccionar la instancia → menú **Edit Security Groups**
5. Quitar `default` si se desea, y añadir `web-only`

---

### ⚖️ Comparativa CLI vs Horizon

| Acción                    | CLI (openstack)           | Horizon                       |
| ------------------------- | ------------------------- | ----------------------------- |
| Crear grupo               | ✅ Preciso y rápido        | ✅ Intuitivo pero más lento    |
| Añadir reglas específicas | ✅ Control total con flags | ⚠️ Puede ser confuso con ICMP |
| Asignar grupo a instancia | ✅ En un comando           | ✅ Desde menú visual           |

---

### 🧹 Retos para el alumno

1. Crear un grupo llamado `solo-ssh` que permita **únicamente TCP/22** (SSH).
2. Quitar el grupo `default` de una instancia y dejarla solo con `solo-ssh`.
3. Verificar que:

   * `ping` desde fuera falla
   * HTTP (puerto 80) falla
   * Solo responde si se escucha algo en el 22 (puede usarse `nc -lk -p 22 -e /bin/echo` en CirrOS)

---

### ✅ Validaciones esperadas

* Las reglas creadas están correctamente asociadas al grupo
* La instancia solo responde en los puertos definidos
* Las pruebas con `nc -vz` o `curl` desde el exterior confirman el filtrado
* Si se remueve `default`, ICMP deja de estar permitido por defecto

---

## 🤩 Reto final (funcional): Hacer pública una instancia **solo por puerto 80** y bloquear el resto

**Escenario:**
Una instancia ejecuta un servicio en el puerto 80 (usando `nc`) y no debe exponer ningún otro puerto (ni ICMP ni 22).

**Objetivo:**
➡️ Configurar un Security Group que permita solo el tráfico HTTP desde el exterior y aplicar la política a una instancia existente.

### 📌 Pistas

* Crea un grupo nuevo sin reglas por defecto.
* Añade una única regla para TCP/80.
* Usa `nc -lk -p 80 -e /bin/echo` en CirrOS para simular servicio.
* Desde tu host, prueba:

  ```bash
  nc -vz <FloatingIP> 80   # debería conectar
  nc -vz <FloatingIP> 22   # debería fallar
  ping <FloatingIP>        # debería fallar
  ```
