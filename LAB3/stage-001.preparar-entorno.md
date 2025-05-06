### Fase 1: Preparación del entorno

**🎯 Objetivos:**
- Crear namespaces de ejemplo (`dev`, `prod`)
- Desplegar dos apps:
  - Una expuesta vía NodePort (servicio web A)
  - Otra que se conecta a ella desde otro namespace (servicio B)

---

**📝 Descripción:**
Partimos de un clúster Kind operativo accesible mediante Lens y kubectl. Kind incluye por defecto un **CNI básico** que permite la comunicación entre pods y servicios, pero **no implementa soporte para NetworkPolicies** ni aislamiento de red avanzado.

🔎 **¿Qué es un CNI?**
Un *Container Network Interface (CNI)* es el componente responsable de conectar los pods a la red y definir cómo se comunican entre sí. Kubernetes requiere un CNI funcional para asignar IPs a los pods y permitir tráfico entre ellos.

➡️ En esta fase, usamos el CNI por defecto de Kind, suficiente para validar conectividad básica y exponer servicios. En fases posteriores (como la Fase 4), sustituiremos este CNI por **Calico** para poder aplicar políticas de red y simular escenarios de seguridad reales.

---

**🔧 Pasos detallados:**

✅ 1. Crear namespaces:
```bash
kubectl create ns dev
kubectl create ns prod
```

✅ 2. Desplegar App A en `dev` (servicio web):
Archivo: `dev-app-a.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-a
  namespace: dev
  labels:
    app: app-a
spec:
  containers:
  - name: web
    image: nginxdemos/hello
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app-a
  namespace: dev
spec:
  type: NodePort
  selector:
    app: app-a
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```
```bash
kubectl apply -f dev-app-a.yaml
```

✅ 3. Desplegar App B en `prod` (cliente curl):
Archivo: `prod-app-b.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-b
  namespace: prod
spec:
  containers:
  - name: curl
    image: curlimages/curl
    command: ["sleep", "infinity"]
```
```bash
kubectl apply -f prod-app-b.yaml
```

✅ 4. Probar acceso desde App B a App A:
```bash
kubectl exec -n prod app-b -- curl http://app-a.dev.svc.cluster.local
```

✅ 5. Probar acceso desde el host:
```bash
curl http://localhost:30080
```

---

**💪 Ejercicio guiado completo arriba.**

---

**🎯 Reto para el alumno:**
- Crea un tercer namespace llamado `test`
- Despliega una nueva app (App C) en ese namespace con imagen `nginx`
- Exponla como NodePort en el puerto `30081`
- Verifica que sea accesible desde `localhost:30081`
- Valida acceso desde `app-b` hacia `app-c.test.svc.cluster.local`

Entrega esperada: archivo `test-app-c.yaml` y comandos utilizados.

---

**✅ Validaciones:**
- `kubectl get namespaces` → aparecen `dev`, `prod`, `test`
- `kubectl get pods -A` → todos los pods Running
- Acceso a app A vía `localhost:30080`
- Desde `app-b`, acceso a `app-a.dev.svc.cluster.local`
- Reto: acceso a `app-c.test.svc.cluster.local` desde `app-b`

---
