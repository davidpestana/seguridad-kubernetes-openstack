## 🛡️ Temario Replanteado: Ciberseguridad en Kubernetes y OpenStack (25h)

### 🧹 Sesión 1: Fundamentos de virtualización segura y arquitecturas modernas

* Ciberseguridad en infraestructuras ENS: principios básicos
* Arquitectura y componentes clave de OpenStack y Kubernetes
* Diferencias, sinergias y aplicación conjunta

**Laboratorio:**

* Puesta en marcha de entornos base:

  * Vagrant + DevStack (OpenStack)
  * Docker + Kind (Kubernetes)
* Validación de red y conectividad inicial

---

### 🔐 Sesión 2: Seguridad de red y control de acceso en OpenStack

* [Neutron y segmentación de red](https://neutron-y-segmentacion-d-6zqf0i6.gamma.site/)
* [Floating IPs, routers y seguridad norte-sur](https://floating-ips-routers-y-s-m7eeav7.gamma.site/)
* [Introducción a Keystone y políticas IAM](https://keystone-y-politicas-iam-7ynm50h.gamma.site/)

**Laboratorio:**

* Creación de redes internas y externas
* Seguridad con Security Groups y reglas personalizadas
* Asignación y uso de Floating IP

** [Arquitecturas multi-site y federación](https://arquitecturas-multi-siti-gqfzx2g.gamma.site/)
---

### 🔐 Sesión 3: Seguridad en Kubernetes: RBAC, políticas y aislamiento

* [Principios de seguridad en Kubernetes: RBAC, API server, namespaces](https://seguridad-en-kubernetes--wmh589y.gamma.site/)
* [Network Policies: control este-oeste](https://politicas-de-red-en-kube-jbmg3s5.gamma.site/)
* [Gestión de secretos y buenas prácticas](https://gestion-segura-de-secret-qzz7i57.gamma.site/)

**Laboratorio:**

* Aplicación de RBAC y namespaces
* Políticas de red con Calico
* Uso seguro de Kubernetes Secrets

---

### 🔍 Sesión 4: Observabilidad, trazabilidad y detección de amenazas

* [Fundamentos de logging y métricas en entornos seguros](https://observabilidad-en-cibers-fwcoecp.gamma.site/)
* [Introducción a Falco, Prometheus, Grafana](https://observabilidad-con-falco-5xvrkr7.gamma.site/)
* [Análisis forense básico y respuesta a incidentes](https://analisis-forense-y-respu-3044mtz.gamma.site/)

**Laboratorio:**

* Instalación y configuración de Falco
* Visualización básica con Grafana y Prometheus
* Simulación de eventos sospechosos

---

### 🔗 Sesión 5: Entorno híbrido y validación de prácticas seguras

* [OpenStack como IaaS para Kubernetes (nociones de interoperabilidad)](https://openstack-como-base-iaas-pea3vck.gamma.site/)
* [Comparativa de gestión y seguridad](https://comparativa-entre-openst-xdi8z6w.gamma.site/)
* [Validación de cumplimiento (checklist ENS)](https://guia-de-cumplimiento-ens-yopc054.gamma.site/)

**Laboratorio Final:**

* Despliegue conjunto de una app en OpenStack + Kubernetes
* Evaluación de seguridad: red, acceso, trazabilidad
* Revisión final y cierre
