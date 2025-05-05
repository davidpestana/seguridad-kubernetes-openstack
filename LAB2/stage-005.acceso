## 🔧 Fase 5 – Validación del tráfico norte-sur mediante SSH y HTTP

**🌟 Objetivo:**
Comprobar el comportamiento real del tráfico norte-sur en OpenStack, evaluando el acceso a instancias con Floating IP usando herramientas como `ping`, `nc`, y `curl` desde fuera del entorno virtualizado.

**📜 Descripción:**
En esta fase se evaluará la conectividad efectiva a dos instancias CirrOS (`app1` y `app2`) desde el exterior. Se pondrá en práctica lo aprendido en fases anteriores combinando: configuración de red, asignación de Floating IP y reglas de Security Groups. El objetivo es validar tanto acceso como bloqueo de puertos según configuración deseada en escenarios diferenciados por instancia.

---

### 🧭 Acciones desde CLI

1. **Verificar las IP públicas de ambas instancias:**

   ```bash
   openstack server show app1 -c addresses
   openstack server show app2 -c addresses
   ```

2. **Desde tu máquina (fuera de OpenStack):**

   ```bash
   ping <FloatingIP-app1>
   ping <FloatingIP-app2>
   nc -vz <FloatingIP-app1> 80
   nc -vz <FloatingIP-app2> 80
   curl http://<FloatingIP-app1>
   curl http://<FloatingIP-app2>
   ```

3. **Simular servicios desde ambas instancias:**
   En la consola de `app1` y `app2`:

   ```bash
   nc -lk -p 80 -e /bin/echo
   nc -lk -p 22 -e /bin/echo
   ```

4. **Asignar Security Groups distintos:**

   * `app1` con `web-only`
   * `app2` con un grupo abierto inicialmente para validación comparada

---

### 🧹 Retos para el alumno

1. Quitar todas las reglas del grupo de `app1` y observar los efectos
2. Permitir solo puerto 22 en `app1`: ¿puedes acceder al servicio `nc` en ese puerto?
3. Comprobar qué ocurre si hay reglas pero no servicio escuchando en `app2`
4. Validar aislamiento efectivo entre `app1` y `app2` al aplicar reglas distintas

---

### ✅ Validaciones esperadas

* El tráfico ICMP, TCP 80 o TCP 22 solo se permite si hay reglas explícitas
* Cada instancia responde o bloquea correctamente según su configuración
* La consola de Horizon muestra diferencias de comportamiento entre `app1` y `app2`

---

## 🤩 Reto final (funcional): Validar un escenario de aislamiento por mantenimiento

**Escenario:**
El equipo de sistemas ha solicitado realizar tareas de mantenimiento sobre `app1`. Mientras tanto, `app1` debe dejar de ser accesible desde el exterior, pero seguir operativa internamente. `app2` debe continuar ofreciendo su servicio web públicamente.

**Objetivo:**
➡️ Aplicar restricciones temporales de acceso desde el exterior solo a `app1`, sin afectar su conectividad interna ni detenerla.

### 📌 Pistas

* No elimines la Floating IP de `app1` ni detengas su instancia
* Usa Security Groups para bloquear solo tráfico entrante desde el exterior
* Verifica que desde `app2` (misma red) puedes seguir accediendo a `app1`
* Desde tu host, `app1` debe dejar de responder, mientras `app2` sigue activa
