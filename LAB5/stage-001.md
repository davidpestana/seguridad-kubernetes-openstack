### 🔹 Fase 1: Despliegue manual de clúster Kubernetes sobre OpenStack

#### 🎯 Objetivos

* Entender cómo lanzar VMs y construir un clúster Kubernetes paso a paso usando OpenStack
* Configurar el nodo maestro y al menos un nodo de trabajo con `kubeadm`
* Validar el acceso a la API del clúster y comprobar su funcionalidad básica

#### 📾 Descripción

Los alumnos operan directamente desde Horizon o CLI. Primero suben una imagen base de Ubuntu, luego crean un keypair SSH y lanzan 2 VMs. En la primera, instalan Kubernetes con `kubeadm init`, exponen la API, y después configuran la segunda VM como nodo con `kubeadm join`. El acceso se hace por Floating IP y se prueba la respuesta del clúster.

#### 🧼 Pasos

1. Subir imagen Ubuntu (si no está disponible) a Glance
2. Crear keypair desde Horizon y descargarlo
3. Crear red interna y router si no existen
4. Lanzar 2 VMs (`k8s-master`, `k8s-node1`) en la red interna
5. Asignar Floating IP al `k8s-master`
6. Instalar Kubernetes en `k8s-master` (swap off, containerd, kubeadm init)
7. Obtener el token y unir `k8s-node1` con `kubeadm join`
8. Verificar `kubectl get nodes` desde el `master`
9. Desde el host o `tower`, acceder a la API del clúster vía IP pública

#### ⚠️ Retos

* Errores en la subida o selección de la imagen Ubuntu
* Red mal configurada (sin salida a Internet, sin conectividad entre nodos)
* Fallos en `kubeadm init` por requisitos no cumplidos (swap, containerd, iptables)
* El `join` puede fallar si los puertos están cerrados por defecto

#### ✅ Validaciones

* SSH funcional a ambas VMs
* `kubeadm init` finalizado con kubeconfig generado
* `kubeadm join` exitoso en el nodo
* `kubectl get nodes` muestra ambos nodos en estado `Ready`
* Acceso a la API desde el exterior (host) vía curl/kubectl

#### 💡 Bonus (no ejecutado, solo entregado)

* Se entrega ejemplo de playbook Ansible que podría automatizar todo el proceso
* Discusión sobre cómo se lanzaría desde la máquina `tower`
* Se deja como tarea voluntaria para reproducir en otro entorno
