## Canvas: Hardware Container Runner - Estado Actual y Tendencias

### ⚙️ Objetivo

Estaddo del arte a 2025 de fabricantes que ofrezcan ejecución de contenedores de forma nativa, eliminando o minimizando el sistema operativo tradicional.

---

### 🔍 Contexto

La ejecución nativa de contenedores directamente sobre hardware es una evolución natural para cargas de trabajo cloud-native, edge computing y entornos de alta seguridad. El objetivo es:

* Reducir la latencia de arranque.
* Minimizar la superficie de ataque.
* Aumentar el rendimiento eliminando capas intermedias.

---

### 🏢 Iniciativas Relevantes

#### 1. AWS Firecracker (con Bare Metal)

* **Tecnología**: MicroVMs optimizadas con KVM.
* **Uso**: AWS Lambda y Fargate.
* **Ventajas**: Arranque < 125ms, consumo de memoria muy bajo.
* **Notas**: No es hardware puro, pero reduce el sistema operativo al mínimo necesario.

#### 2. Kata Containers + Bare Metal

* **Tecnología**: Contenedores con aislamiento fuerte sobre microVMs.
* **Integración**: Lenovo, Intel y otros lo integran en soluciones edge.
* **Notas**: Apropiado para entornos con alto requerimiento de aislamiento.

#### 3. Cloud Hypervisor / NEMU (Intel)

* **Tecnología**: VMM minimalistas.
* **Objetivo**: Ejecutar cargas virtualizadas directamente desde UEFI, sin OS completo.
* **Notas**: En fase de adopción temprana, pero promisorios en edge y telecom.

#### 4. Oxide Computer

* **Producto**: Servidores cloud con stack integrado (hardware + hypervisor).
* **Diseño**: Seguridad por defecto, boot sin BIOS convencional.
* **Notas**: No elimina el OS, pero lo abstrae y lo optimiza fuertemente.

#### 5. Unikernels / IncludeOS

* **Modelo**: Aplicación como imagen bootable sin sistema operativo.
* **Uso**: Nichos de telecomunicaciones, defensa, IoT.
* **Limitaciones**: Difícil debugging, soporte limitado de librerías, no multiusuario.

---

### 🚫 Limitaciones actuales

* Aún no existe un "container runner hardware" comercializado como tal.
* La seguridad, el soporte y el ecosistema están más maduros en entornos tradicionales.
* El mantenimiento y la observabilidad son más difíciles con stacks reducidos o eliminando OS.

---

### 🔄 Tendencias

* Contenedores + MicroVMs como punto intermedio viable (Firecracker, Kata).
* Eliminación progresiva de capas: BIOS, OS generalista, incluso kernel genérico.
* Adopción de hardware seguro (TPM, enclaves) para cargas más aisladas.

---

### 🔹 Conclusión

Aunque no existe un "container runner" hardware puro en el mercado, hay una evolución clara hacia:

* Simplificar los servidores para ejecutar contenedores sin overhead.
* Usar firmware o VMMs especializados como base.
* Replantear el concepto de sistema operativo en entornos cloud-native.

Es probable que en los próximos años veamos hardware comercial que ofrezca contenedores como carga bootable sin OS tradicional.
