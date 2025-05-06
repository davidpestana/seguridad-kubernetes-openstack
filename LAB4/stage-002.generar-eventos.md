## 🧪 Fase 2: Generación de eventos sospechosos y validación con Falco

### 🌟 Objetivo

Forzar la aparición de eventos que Falco pueda detectar como sospechosos y validar que se generen alertas correctamente.

---

### 📋 Descripción

Falco incluye reglas predefinidas para detectar comportamientos como ejecución de shells, lectura de archivos sensibles o conexiones de red inusuales. Esta fase provocará algunos de estos comportamientos dentro de un pod.

---

### 🔭 Pasos

1. **Crear un pod temporal con acceso básico al sistema:**

   ```bash
   kubectl run testpod --rm -i --tty --image=alpine -- sh
   ```

2. **Dentro del pod, ejecutar comandos considerados sospechosos:**

   ```sh
   ls /etc
   cat /etc/shadow
   nc google.com 80
   ```

3. **Salir del pod y revisar los logs de Falco:**

   ```bash
   kubectl logs -n falco deploy/falco | grep -i "Warning"
   ```

4. **(Opcional) Ver alertas en la interfaz web de Falcosidekick:**

   ```bash
   kubectl port-forward svc/falco-falcosidekick-ui 2802:2802 -n falco
   ```

   Luego accede a: [http://localhost:2802](http://localhost:2802)

---

### ⚠️ Retos

* Algunas reglas pueden no activarse si el evento no es considerado "peligroso" por Falco.
* Puede requerirse ajuste en reglas para entornos containerizados simples como Kind.

---

### ✅ Validaciones

* Presencia de eventos como:

  * Shell spawned in a container
  * Sensitive file read (e.g. /etc/shadow)
  * Outbound connection detected
* Alertas visibles en logs o en Falcosidekick Web UI
* Confirmación de que Falco responde activamente al comportamiento sospechoso
