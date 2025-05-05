## 🔧 Fase 4 – Asignación y uso de Floating IP para una instancia

**🌟 Objetivo:**
Asignar una Floating IP a una instancia para permitir el acceso desde el exterior, validando su funcionamiento junto con los Security Groups y la red externa.

**📜 Descripción:**
Esta fase cubre cómo obtener una Floating IP de la red externa y asociarla correctamente a una instancia activa. Se demostrará el impacto directo que tienen los Security Groups sobre el tráfico, y se realizarán pruebas de conectividad desde fuera del entorno OpenStack (host físico o red externa).

---

### 🧭 Acciones desde CLI

1. **Crear una Floating IP:**

   ```bash
   openstack floating ip create red-externa
   ```

   > Esto reservará una IP pública del rango de `red-externa`.

2. **Ver las IPs disponibles:**

   ```bash
   openstack floating ip list
   ```

3. **Asociar la Floating IP a la instancia:**

   ```bash
   openstack server add floating ip cirros-test <ip_flotante>
   ```

4. **Verificar la asociación:**

   ```bash
   openstack server show cirros-test -f json | jq '.addresses'
   ```

---

### 💥 Acciones desde Horizon

* Ir a **Project > Network > Floating IPs**
* Crear una nueva IP seleccionando `red-externa`
* Ir a **Instances > Associate Floating IP** desde la instancia deseada
* Verificar en la vista de instancia que la IP pública figura junto a la privada

---

### 🧹 Retos para el alumno

1. Reservar dos Floating IPs y probar a moverlas entre instancias activas
2. Comprobar si una IP se puede liberar mientras está asociada
3. Acceder por HTTP a una instancia con el grupo `web-only` desde tu máquina externa
4. Intentar acceder por ICMP/22 si las reglas no lo permiten: ¿conectividad denegada?

---

### ✅ Validaciones esperadas

* La IP flotante está visible en la lista de `openstack floating ip list`
* La IP figura asociada a la instancia correctamente
* La conexión desde fuera depende exclusivamente de las reglas del Security Group
* Un servicio activo en el puerto 80 responde si el grupo `web-only` está aplicado

---

## 🤩 Reto final (funcional): Desvincular y reasignar una Floating IP dinámicamente

**Escenario:**
Dos instancias CirrOS están desplegadas, pero solo una puede estar expuesta a Internet en cada momento. Quieres usar una única Floating IP que se pueda mover entre ambas.

**Objetivo:**
➡️ Asociar dinámicamente una Floating IP a una u otra instancia según necesidad.

### 📌 Pistas

* Usa `openstack server remove floating ip` y luego `add floating ip`
* Lanza `nc` en ambas instancias por el mismo puerto (por ejemplo, 80)
* Desde tu host, haz `curl` o `nc -vz` a la IP para validar quién está activo
* Valida que el cambio no requiere reiniciar la instancia
