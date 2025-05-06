## 🔧 Fase 8 – Despliegue de aplicación multi-nodo con múltiples NICs

**🌟 Objetivo:**
Crear instancias con múltiples interfaces de red, conectadas simultáneamente a redes privadas y compartidas, para simular escenarios de aplicaciones distribuidas o bastionadas.

**📜 Descripción:**
Esta fase explora cómo conectar una misma instancia a varias redes y cómo se gestiona el enrutamiento y la seguridad en entornos multi-NIC. Se desplegarán dos instancias: una pública (frontend) y otra privada (backend), conectadas por una red compartida interna.

---

### 🧭 Acciones desde CLI

1. **Crear una red interna compartida para conectar ambas instancias:**

   ```bash
   openstack network create red-app-interna --share
   openstack subnet create --network red-app-interna \
     --subnet-range 10.100.0.0/24 \
     subred-app-interna
   ```

2. **Lanzar la instancia frontend con 2 NICs:**

   ```bash
   openstack server create frontend \
     --image cirros \
     --flavor m1.tiny \
     --nic net-id=<red-externa> \
     --nic net-id=<red-app-interna> \
     --security-group web-only
   ```

3. **Lanzar la instancia backend solo en red interna:**

   ```bash
   openstack server create backend \
     --image cirros \
     --flavor m1.tiny \
     --nic net-id=<red-app-interna> \
     --security-group default
   ```

4. **Asociar Floating IP solo al frontend:**

   ```bash
   openstack floating ip create red-externa
   openstack server add floating ip frontend <ip>
   ```

---

### 💥 Acciones desde Horizon

* Crear red compartida (Project > Network > Networks)
* Al lanzar instancia: elegir múltiples NICs (Shift + clic)
* Verificar en la pestaña de interfaces que aparecen ambas
* Comprobar direcciones IP desde consola o panel

---

### 🧹 Retos para el alumno

1. Conectarse por Floating IP a `frontend` y desde ahí hacer ping a `backend`
2. Intentar acceder directamente a `backend` desde fuera: ¿bloqueado?
3. Comprobar si `backend` tiene salida a Internet o no
4. Cambiar el SG de `backend` para permitir respuesta desde `frontend`

---

### ✅ Validaciones esperadas

* `frontend` tiene dos IPs (una pública y otra interna)
* `backend` solo tiene IP interna y no responde desde el exterior
* La comunicación entre ambos funciona por la red compartida
* Los SGs determinan si hay tráfico permitido entre ellos

---

## 🤩 Reto final (funcional): Simular una arquitectura bastionada

**Escenario:**
Solo el nodo `frontend` está expuesto al exterior. `backend` debe permanecer inaccesible directamente pero recibir tráfico interno legítimo desde el frontend para operaciones backend.

**Objetivo:**
➡️ Configurar SGs y redes para permitir únicamente ese patrón de comunicación.

### 📌 Pistas

* Crea una regla en el SG de `backend` para aceptar tráfico desde la IP interna de `frontend`
* Usa `nc` para simular servicios entre ellos
* Asegúrate de que `backend` no tiene Floating IP ni reglas globales abiertas
