## Opciones de Ejecución Segura de Contenedores en Kubernetes

### Objetivo

Explorar soluciones que permitan ocultar o proteger procesos ejecutados en contenedores dentro de Kubernetes, impidiendo el análisis o hookeo desde el anfitrión.

---

### 1. Kata Containers (con RuntimeClass)

* **Tipo de aislamiento:** Virtualización ligera (microVMs).
* **Nivel de protección:**

  * El proceso no comparte kernel ni espacio de memoria con el host.
  * Dificulta mucho el análisis y hooking desde el nodo anfitrión.
* **Requisitos:**

  * Instalación de Kata Containers en los nodos.
  * Configuración de `RuntimeClass` en Kubernetes (`handler: kata`).
  * Compatible con `containerd` o `CRI-O`.
* **¿Requiere hardware especializado?:** ❌ No.
* **¿Listo para producción?:** ✅ Sí.

#### Ejemplo de configuración de `RuntimeClass` y Pod con Kata

```yaml
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: kata
handler: kata
---
apiVersion: v1
kind: Pod
metadata:
  name: ejemplo-kata
spec:
  runtimeClassName: kata
  containers:
  - name: secure-app
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
```

---

### 2. Confidential Containers (CoCo)

* **Tipo de aislamiento:** MicroVMs + Cifrado de memoria en hardware.
* **Nivel de protección:**

  * El host, hipervisor y kernel **no pueden leer el contenido en memoria del contenedor**.
  * Protección contra ataques desde el propio nodo o desde operadores cloud maliciosos.
* **Requisitos:**

  * Soporte de `Kata + SEV` (AMD) o `Kata + TDX` (Intel).
  * Configuración de `RuntimeClass` (`handler: kata-cc`).
  * Hardware con soporte de **Trusted Execution Environments (TEE)**:

    * AMD SEV/SEV-ES/SEV-SNP.
    * Intel SGX/TDX.
* **¿Requiere hardware especializado?:** ✅ Sí.
* **¿Listo para producción?:** ⚠️ Parcialmente. Adecuado para PoCs y entornos regulados.

#### Ejemplo de uso de `RuntimeClass` confidencial

```yaml
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: kata-cc
handler: kata-cc
---
apiVersion: v1
kind: Pod
metadata:
  name: ejemplo-confidencial
spec:
  runtimeClassName: kata-cc
  containers:
  - name: secure-app
    image: ghcr.io/confidential-containers/test-image
    command: ["sh", "-c", "echo Hola desde enclave seguro && sleep 3600"]
```

---

### 3. WebAssembly (Wasm) en Kubernetes

* **Tipo de ejecución:** Binarios Wasm en lugar de binarios Linux convencionales.
* **Nivel de protección:**

  * Ejecuta en sandbox diferente al entorno Linux.
  * Menor superficie de ataque.
  * No es ocultamiento, pero dificulta análisis binario tradicional.
* **Requisitos:**

  * Motores Wasm: `wasmtime`, `WasmEdge`, etc.
  * Shim para `containerd` o `krustlet` para integración en Kubernetes.
* **¿Requiere hardware especializado?:** ❌ No.
* **¿Listo para producción?:** ⚠️ Parcialmente, adecuado para workloads experimentales o edge.

---

### Comparativa Rápida

| Solución                | Oculta del host | Aislamiento real | Requiere hardware | Preparado para producción |
| ----------------------- | --------------- | ---------------- | ----------------- | ------------------------- |
| Kata Containers         | ✅ Parcial       | ✅ VM ligera      | ❌                 | ✅                         |
| Confidential Containers | ✅ Completo      | ✅ VM + cifrado   | ✅                 | ⚠️ Parcial                |
| WebAssembly             | ❌               | ⚠️ Sandbox       | ❌                 | ⚠️ Parcial                |

---

### Conclusión

Para proteger procesos de forma efectiva en Kubernetes frente al análisis desde el host:

* **Kata Containers** es la solución más madura sin necesidad de hardware especial.
* **Confidential Containers** es la opción más segura, pero exige hardware TEE.
* **WebAssembly** puede dificultar el análisis, pero no protege realmente contra un host comprometido.