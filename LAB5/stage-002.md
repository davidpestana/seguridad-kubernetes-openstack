### 🔹 Fase 2: Validaciones de red y control de acceso en OpenStack

#### 🎯 Objetivos

* Comprobar el funcionamiento de las medidas de aislamiento y acceso en la red de OpenStack
* Simular el control de exposición de la API de Kubernetes mediante Floating IP, Security Groups y tablas de rutas
* Realizar pruebas de conectividad y bloqueo para validar reglas de seguridad

#### 📾 Descripción

En esta fase se trabaja exclusivamente desde OpenStack para probar cómo el entorno de red afecta al acceso a la API del clúster. Se modifica el estado de las reglas de Security Groups, se eliminan o reconfiguran Floating IPs, y se editan rutas para observar el impacto en la accesibilidad del plano de control de Kubernetes. Se simulan ataques o cortes de acceso controlados y se observa el comportamiento del clúster.

#### 🧼 Pasos

1. Comprobar conectividad normal hacia la API del clúster desde el host o la máquina `tower` (curl, kubectl)
2. Modificar el Security Group del `k8s-master` para denegar el puerto 6443
3. Intentar acceso a la API y validar el bloqueo
4. Reabrir el puerto en el SG y verificar recuperación del acceso
5. Eliminar la Floating IP y comprobar que ya no se puede acceder desde fuera
6. Asignar una nueva Floating IP y revalidar acceso
7. Crear reglas en la tabla de rutas que limiten el tráfico entre redes
8. Validar el comportamiento de los nodos ante cambios de red (por ejemplo, cortar tráfico al `k8s-node1`)

#### ⚠️ Retos

* Configuración incorrecta de reglas SG que bloqueen el acceso completamente
* No asignar correctamente las Floating IPs puede impedir pruebas
* Cambios en las rutas pueden afectar también a Horizon si se aplican globalmente
* Dificultad para interpretar los errores de conectividad si no se diferencian bien niveles (K8s vs red)

#### ✅ Validaciones

* Acceso denegado y luego restaurado mediante reglas SG
* Acceso eliminado y restaurado tras quitar y volver a asignar Floating IP
* Comprobación de que la red interna funciona entre nodos
* `kubectl get nodes` solo funcional si el acceso a la API está permitido
* Validación de escenarios de acceso limitado (solo desde una red)

#### 💡 Bonus

* Se puede proponer un escenario con un SG "cerrado por defecto" y sólo permitir lo mínimo necesario
* Mostrar cómo se puede proteger un cluster expuesto con acceso público mediante combinación de reglas SG y flotantes
* Comparar comportamiento con el cluster Kind que no depende de SGs, solo de firewall local
