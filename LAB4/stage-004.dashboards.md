## 🧪 Fase 4: Exposición de dashboards y primeros datos de métrica

### 🌟 Objetivo

Acceder a los dashboards incluidos con Grafana y validar que se están recolectando y visualizando métricas del clúster Kind correctamente.

---

### 📋 Descripción

El chart `kube-prometheus-stack` instala dashboards preconfigurados que permiten ver información detallada del estado del clúster, uso de CPU/memoria, estado de los pods, y otras métricas clave para observabilidad y seguridad.

---

### 🔭 Pasos

1. **Entrar a Grafana desde el navegador:**
   [http://localhost:3000](http://localhost:3000)
   Usuario: `admin` / Contraseña: `prom-operator`

2. **Ir a "Dashboards > Browse" y localizar:**

   * Kubernetes / Compute Resources / Cluster
   * Kubernetes / Networking / Namespace (para Falco)
   * Prometheus / Stats

3. **Filtrar por namespace para ver métricas de Falco y otros servicios:**

   * Usar selector: `namespace = falco` o `namespace = monitoring`

4. **Validar series activas y métricas en tiempo real:**

   * Uso de CPU por pod
   * Consumo de memoria de Falco
   * Actividad en los nodos (aunque Kind ejecuta todo en 1 contenedor)

---

### ⚠️ Retos

* En Kind solo hay un nodo y pocos pods, lo que puede limitar los datos.
* Puede requerirse generar carga para ver cambios significativos.

---

### ✅ Validaciones

* Dashboards visibles y cargando correctamente
* Métricas de pods (Falco incluido) accesibles
* Series activas confirmadas en Prometheus
