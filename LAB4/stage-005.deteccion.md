## 🧪 Fase 5: Simulación de patrones de ataque para observabilidad cruzada

### 🌟 Objetivo

Producir actividad anómala en el clúster que pueda ser detectada tanto por Falco como por Prometheus/Grafana, comprobando la trazabilidad completa.

---

### 📋 Descripción

Se simularán eventos sospechosos como conexiones externas, uso elevado de CPU, y lecturas de archivos sensibles para activar alertas y visualizar correlación de eventos.

---

### 🔭 Pasos

1. **Crear un pod de prueba con herramientas de red:**

   ```bash
   kubectl run attacker --rm -i --tty --image=alpine -- sh
   apk add curl busybox-extras
   ```

2. **Realizar acciones sospechosas desde el pod:**

   ```sh
   nc -zv google.com 80
   cat /etc/shadow
   for i in $(seq 1 100000); do echo "load"; done
   ```

3. **Observar logs en Falco:**

   ```bash
   kubectl logs -n falco deploy/falco | grep -i Warning
   ```

4. **Acceder a dashboards en Grafana:**

   * Buscar picos en CPU y eventos en `namespace = default`
   * Revisar dashboards de recursos y alertas

---

### ⚠️ Retos

* Kind puede limitar la expresividad del uso de CPU/IO en pruebas
* Algunos eventos pueden ser filtrados si el sistema no considera peligro

---

### ✅ Validaciones

* Logs de Falco muestran eventos sospechosos
* Dashboards de Grafana muestran correlación en el tiempo
* Posibilidad de explicar "incidente" desde múltiples capas (detección y métricas)
