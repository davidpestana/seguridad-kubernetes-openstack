## 🧪 Fase 6: Revisión final y trazabilidad completa del incidente

### 🌟 Objetivo

Recapitular el incidente generado, reconstruir la secuencia de eventos, y validar que el sistema ofrece trazabilidad completa desde la detección hasta la visualización.

---

### 📋 Descripción

A partir de los eventos generados en la fase anterior, se revisa la información obtenida desde Falco, Grafana y los logs, simulando una investigación forense básica.

---

### 🔭 Pasos

1. **Identificar el momento del incidente en Grafana:**

   * Revisar dashboards para localizar el pico de actividad sospechosa
   * Notar uso de CPU, actividad inusual en pods, conexiones de red

2. **Cruzar información con los logs de Falco:**

   * Verificar qué eventos se detectaron: shell, lectura de archivo, conexión externa

3. **Comparar tiempos y secuencia de eventos:**

   * Confirmar que los eventos en Falco coinciden con los picos en métricas
   * Trazar causa-efecto

4. **Documentar la trazabilidad del incidente:**

   * Desde el comando ejecutado hasta la alerta y visualización gráfica

5. **(Opcional) Proponer mejoras:**

   * Crear una regla personalizada en Falco
   * Añadir alerta en Prometheus/Alertmanager

---

### ✅ Validaciones

* Cronología del incidente completa y comprensible
* Confirmación de que Falco detecta y Grafana refleja el impacto
* Capacidad de justificar qué ocurrió, cuándo, y dónde
