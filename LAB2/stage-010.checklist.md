## 🔧 Fase 10 – Revisión final: conectividad, trazabilidad y checklist de seguridad

**🌟 Objetivo:**
Realizar una validación completa del entorno desplegado: comprobar conectividad, trazabilidad de acceso y cumplimiento de prácticas seguras básicas en redes y control de acceso.

**📜 Descripción:**
En esta última fase consolidamos todo lo aprendido. Se revisará si las redes están correctamente conectadas, los routers permiten salida, las reglas de seguridad se aplican como se espera y las instancias respetan los patrones de aislamiento y visibilidad definidos.

---

### ✅ Revisión de conectividad

1. Verificar que cada instancia tiene conectividad:

   ```bash
   ping 8.8.8.8        # desde instancias con salida a internet
   ping <otras VMs>   # conectividad interna entre instancias
   ```

2. Validar Floating IPs:

   ```bash
   openstack floating ip list
   nc -vz <floating_ip> 80  # acceso externo
   ```

3. Verificar rutas y NAT (desde consola):

   ```bash
   ip route
   curl ifconfig.me
   ```

---

### 🔐 Revisión de reglas de seguridad

1. Revisar grupos activos por instancia:

   ```bash
   openstack server show <vm> -c security_groups
   openstack security group show <sg>
   ```

2. Verificar que los SG cumplen políticas:

   * Instancias expuestas solo por los puertos necesarios
   * Instancias backend no tienen Floating IP
   * ICMP bloqueado donde no es requerido

3. Validar comportamiento desde consola:

   ```bash
   nc -vz <ip> <puerto>
   curl http://<ip>
   ping <ip>
   ```

---

### 🔍 Revisión de trazabilidad y visibilidad

* ¿Qué ve cada usuario desde Horizon? ¿Está limitado a su proyecto?
* ¿Aparecen redes cruzadas o recursos ajenos?
* ¿Hay reglas compartidas sin control?

---

### 🧹 Checklist de cumplimiento

* [ ] Cada instancia pertenece a un proyecto específico
* [ ] Las redes privadas no son compartidas salvo excepción
* [ ] El tráfico permitido está explícitamente definido en los SG
* [ ] Las instancias no necesarias para Internet no tienen Floating IP
* [ ] Hay un router definido con puerta de enlace externa activa

---

## 🤩 Reto final (funcional): Validación cruzada por pares

**Escenario:**
Cada alumno debe validar la configuración de red y seguridad de otro compañero, asegurándose de que su entorno cumple con las buenas prácticas establecidas.

**Objetivo:**
➡️ Comprobar que lo que un alumno ha desplegado es coherente, seguro y funcional según el checklist del curso.

### 📌 Pistas

* Usar CLI y Horizon para revisar reglas y redes
* Probar accesos permitidos y denegados
* Documentar cualquier desviación o hallazgo
* Proponer correcciones y explicarlas al autor
