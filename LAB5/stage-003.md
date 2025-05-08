### 🔹 Fase 3: Validación de trazabilidad y registros de actividad

#### 🎯 Objetivos

* Verificar que las acciones realizadas sobre OpenStack y Kubernetes quedan registradas adecuadamente
* Acceder y analizar logs de eventos relevantes desde ambos entornos
* Comprender la importancia del logging para auditoría y cumplimiento ENS

#### 📾 Descripción

Se revisan los sistemas de logging de OpenStack y Kubernetes para evaluar la trazabilidad de las operaciones realizadas durante el laboratorio. Se accede a los logs de Horizon, Keystone y Neutron en el nodo DevStack, y a los logs del API Server y `kubelet` desde el nodo `k8s-master`. Se simulan acciones de configuración, acceso fallido y cambios de red para forzar eventos que puedan auditarse.

#### 🧼 Pasos

1. En DevStack, acceder a logs de:

   * `/var/log/nova/`
   * `/var/log/keystone/`
   * `/var/log/neutron/`
2. Buscar trazas de creación de instancias, asignación de Floating IPs y cambios de SG
3. En `k8s-master`, revisar:

   * `/var/log/syslog` o `journalctl -u kubelet`
   * Archivos de log del `kube-apiserver` si está habilitado
4. Realizar una modificación de un pod o namespace para forzar un evento
5. Simular un acceso denegado a la API con credenciales erróneas o sin token
6. Verificar que el evento queda registrado en el log correspondiente

#### ⚠️ Retos

* Volumen de logs muy alto, difícil de filtrar sin herramientas adicionales
* Algunos servicios pueden no tener nivel de log configurado adecuadamente
* El clúster puede no tener audit logs habilitados si no se configuró en `kubeadm`
* Los logs de DevStack pueden no persistir tras reinicio de la VM

#### ✅ Validaciones

* Logs de Keystone muestran accesos correctos e incorrectos
* Logs de Neutron muestran cambios en red, asignaciones de IP
* En Kubernetes, acciones como `kubectl apply`, `kubectl delete` aparecen trazadas
* Simulación de error queda registrada (ej: acceso denegado)
* Se discute la ubicación y utilidad de cada log en un entorno real

#### 💡 Bonus

* Propuesta: exportar logs a un sistema externo (rsyslog, ELK) para auditoría
* Explicación de qué debería incluir un sistema SIEM en entornos mixtos
* Mención a `auditd`, `falco` o `opa` como herramientas adicionales para control
