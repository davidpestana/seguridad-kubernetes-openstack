## 🔧 Fase 9 – Resolución DNS interna y acceso a metadata en instancias

**🌟 Objetivo:**
Explorar cómo las instancias obtienen servicios básicos como resolución DNS y acceso a metadata desde el plano de control de OpenStack, sin depender de servicios externos.

**📜 Descripción:**
En esta fase veremos cómo una instancia en OpenStack resuelve nombres DNS y accede a su metadata (IP, hostname, claves SSH, etc.). Se validará que el entorno provee estos servicios y se comprobarán desde dentro de una VM CirrOS o Ubuntu.

---

### 🧭 Acciones desde CLI (dentro de la instancia)

1. **Ver los DNS configurados automáticamente:**

   ```bash
   cat /etc/resolv.conf
   ```

   > Normalmente apunta a un DNS interno gestionado por Neutron (dnsmasq).

2. **Resolver nombres públicos y privados:**

   ```bash
   ping openstack.org
   ping backend
   ```

   > Si hay nombres internos, resolverá por DNS local si están registrados.

3. **Consultar el servicio de metadata:**

   ```bash
   curl http://169.254.169.254/openstack/latest/meta_data.json
   ```

   > Provee detalles de red, nombre de host, claves inyectadas...

---

### 💥 Acciones desde Horizon

* Verificar en la ficha de la instancia que aparece el hostname y la red
* Acceder a la consola y repetir los comandos anteriores

---

### ⚖️ Comparativa de servicios en nube

| Servicio                      | AWS (EC2) | OpenStack                 |
| ----------------------------- | --------- | ------------------------- |
| DNS interno                   | ✅         | ✅ (dnsmasq de Neutron)    |
| Metadata por 169.254.169.254  | ✅         | ✅ (vía nova-api-metadata) |
| Cambiar hostname vía metadata | ✅         | ✅ si está habilitado      |

---

### 🧹 Retos para el alumno

1. Comprobar si CirrOS recibe el DNS correctamente
2. Consultar los datos de metadata desde consola con `curl`
3. Probar a usar `dig` o `nslookup` si está disponible
4. Verificar si una instancia sin Floating IP también accede a metadata

---

### ✅ Validaciones esperadas

* `/etc/resolv.conf` contiene un servidor DNS interno (generalmente .1 del gateway)
* La URL `http://169.254.169.254/...` responde sin errores
* La resolución DNS permite acceso a dominios públicos (si hay salida)

---

## 🤩 Reto final (funcional): Comprobar la inyección de hostname y claves por metadata

**Escenario:**
Has creado una instancia desde Horizon y quieres validar que ha recibido correctamente el nombre de host y las claves públicas SSH definidas en la configuración del proyecto.

**Objetivo:**
➡️ Validar desde la instancia que la metadata refleja correctamente el hostname y que se inyectaron las claves autorizadas.

### 📌 Pistas

* Usa `curl 169.254.169.254/...` y analiza el JSON
* Verifica si `hostname` coincide con el mostrado en Horizon
* Comprueba si la clave pública aparece bajo `public_keys` (si fue definida en el par de claves)
