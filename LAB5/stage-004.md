### 🔹 Fase 4: Evaluación final y cierre del laboratorio

#### 🎯 Objetivos

* Consolidar los aprendizajes del entorno OpenStack + Kubernetes
* Revisar la postura de seguridad del clúster desplegado
* Realizar una validación estructurada basada en ENS

#### 📾 Descripción

Se realiza un repaso final del entorno desplegado, verificando que los aspectos críticos de seguridad están cubiertos: acceso restringido, aislamiento de red, trazabilidad, y configuración segura del clúster. Se utiliza una checklist ENS adaptada al contexto del laboratorio para realizar esta validación. El grupo reflexiona sobre posibles mejoras, automatizaciones futuras y extrapolación a entornos reales.

#### 🧼 Pasos

1. Revisión del control de acceso (keypair, SG, RBAC de Kubernetes)
2. Revisión de configuraciones de red y políticas de aislamiento (NetworkPolicy, SG)
3. Confirmación de presencia de logs y trazabilidad de acciones
4. Evaluación del uso seguro de Kubernetes Secrets y token kubeadm
5. Aplicación de una mini checklist ENS:

   * Autenticación robusta
   * Cifrado de comunicaciones
   * Aislamiento de recursos
   * Registro de eventos
6. Discusión abierta: ¿Qué parte se automatizaría? ¿Qué falta para llevar esto a producción?
7. Entrega del ejemplo de playbook Ansible y análisis de cómo integrarlo con el proceso manual

#### ⚠️ Retos

* No todos los controles ENS pueden implementarse en un entorno reducido
* La validación ENS se adapta, pero no cubre todo el espectro de cumplimiento
* Algunas mejoras propuestas (como KMS, OPA o SIEM) no están implementadas, solo mencionadas

#### ✅ Validaciones

* Se completan todos los puntos del checklist adaptado
* Se identifican puntos fuertes y débiles del entorno
* Se entrega propuesta de mejora basada en automatización
* Se valida la comprensión del flujo OpenStack + Kubernetes + seguridad

#### 💡 Bonus

* Propuesta de roles adicionales en Ansible: `openstack-secure`, `kubeadm-harden`, `log-export`
* Mención a pipelines de CI/CD con validaciones de seguridad previas al despliegue
* Pistas para entornos de producción: clústers con HA, cifrado con KMS, logging centralizado
