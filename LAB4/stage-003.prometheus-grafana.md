## 🧪 Fase 3: Instalación de Prometheus y Grafana vía kube-prometheus-stack

### 🌟 Objetivo

Instrumentar el clúster Kind con Prometheus y Grafana para recolectar métricas, visualizarlas y preparar el entorno para generar alertas.

---

### 📋 Descripción

Usaremos el Helm chart oficial de `kube-prometheus-stack`, que incluye Prometheus, Alertmanager, node-exporter, kube-state-metrics y Grafana listos para su despliegue en clústeres Kubernetes.

---

### 🔭 Pasos

1. **Agregar el repositorio de Helm:**

   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo update
   ```

2. **Crear namespace de monitorización:**

   ```bash
   kubectl create ns monitoring
   ```

3. **Instalar el chart kube-prometheus-stack:**

   ```bash
   helm install monitoring prometheus-community/kube-prometheus-stack \
     --namespace monitoring
   ```

4. **Verificar que los pods estén levantados:**

   ```bash
   kubectl get pods -n monitoring
   ```

5. **Exponer Grafana localmente:**

   ```bash
   kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
   ```

   Accede desde el navegador: [http://localhost:3000](http://localhost:3000)

   Usuario/contraseña por defecto: `admin / prom-operator`

---

### ⚠️ Retos

* Algunos pods pueden tardar en arrancar en Kind por recursos o afinidad.
* Si hay colisiones de puertos, puede requerir ajustes en el port-forwarding.

---

### ✅ Validaciones

* Acceso a la interfaz de Grafana funcionando
* Dashboards preconfigurados con datos de nodos, pods y servicios
* Prometheus accesible en: `http://localhost:9090` (con port-forward opcional)
