## 🧪 Fase 1: Instalación de Falco en Kind usando Helm

### 🌟 Objetivo

Desplegar el agente de seguridad **Falco** en un clúster Kind para monitorizar eventos del sistema y detectar comportamientos sospechosos.

---

### 📋 Descripción

Falco se basa en la captura de syscalls del kernel para generar alertas de seguridad en tiempo real. En entornos con Kind (que usa contenedores dentro de Docker), no todos los métodos de captura están soportados, por lo que usaremos **driver eBPF precompilado** o configuraremos **modo userspace** según sea necesario.

---

### 🔭 Pasos

1. **Instalar Helm (si no está instalado):**

   ```bash
   curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
   ```

2. **Agregar el repositorio de Falco:**

   ```bash
   helm repo add falcosecurity https://falcosecurity.github.io/charts
   helm repo update
   ```

3. **Crear el namespace para Falco:**

   ```bash
   kubectl create ns falco
   ```

4. **Instalar Falco con Helm (modo userspace recomendado para Kind):**

   ```bash
   helm install falco falcosecurity/falco \
     --namespace falco \
     --set falco.driver.kind=modern_ebpf \
     --set falcosidekick.enabled=true \
     --set falcosidekick.webui.enabled=true
   ```

5. **Verificar que los pods estén corriendo:**

   ```bash
   kubectl get pods -n falco
   ```

---

### ⚠️ Retos

* Falco puede no arrancar correctamente en Kind por temas de kernel o cgroups.
* Puede requerir ajustes como `--privileged` si se usan DaemonSets sobre nodos Docker-in-Docker.

---

### ✅ Validaciones

* `kubectl logs -n falco deploy/falco` debe mostrar líneas activas de monitoreo.
* El pod de `falco` debe estar en estado `Running`.
* La salida debe incluir mensajes como:

  ```
  Falco initialized with X rules, driver: ebpf, capturing syscalls...
  ```
