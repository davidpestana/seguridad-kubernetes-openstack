### Fase 2: Definición de RBAC básico

**🎯 Objetivos:**
- Comprender cómo restringir acciones usando RBAC
- Crear Roles, RoleBindings y ServiceAccounts
- Aplicar control de acceso a recursos específicos en namespaces

---

**📝 Descripción:**
En esta fase vamos a simular un entorno multiusuario dentro del clúster. Usaremos **ServiceAccounts** para representar usuarios o servicios y asignaremos permisos con **Roles** y **RoleBindings**. El alumno verá cómo limitar acciones como listar pods o acceder a servicios solo si está autorizado.

---

**🔧 Pasos detallados (ejercicio guiado):**

✅ 1. Crear una ServiceAccount para el namespace `dev`:
```bash
kubectl create serviceaccount viewer -n dev
```

✅ 2. Crear un Role que permita listar pods:
Archivo: `dev-role-pod-reader.yaml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```
```bash
kubectl apply -f dev-role-pod-reader.yaml
```

✅ 3. Asociar la ServiceAccount al Role con un RoleBinding:
Archivo: `dev-rolebinding-pod-reader.yaml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: ServiceAccount
  name: viewer
  namespace: dev
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
```bash
kubectl apply -f dev-rolebinding-pod-reader.yaml
```

✅ 4. Probar el acceso usando un pod que utilice esa ServiceAccount:
Archivo: `test-viewer-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: rbac-test
  namespace: dev
spec:
  serviceAccountName: viewer
  containers:
  - name: cli
    image: bitnami/kubectl
    command: ["sleep", "infinity"]
```
```bash
kubectl apply -f test-viewer-pod.yaml
kubectl exec -n dev rbac-test -- kubectl get pods -n dev
```
✅ Debería funcionar.

✅ 5. Probar acceso fuera del alcance permitido:
```bash
kubectl exec -n dev rbac-test -- kubectl get svc -n dev
```
❌ Debería fallar: no tiene permiso para listar servicios.

---

**🎯 Reto para el alumno:**
- Repite el mismo patrón en el namespace `prod`
- Crea una ServiceAccount llamada `svcviewer`
- Crea un Role que permita listar `services`
- Aplica el RoleBinding correspondiente
- Lanza un pod usando esa ServiceAccount
- Verifica que puede ver `services` pero no `pods`

Entrega esperada: archivos YAML (`Role`, `RoleBinding`, `Pod`) y capturas o comandos de prueba.

---

**✅ Validaciones:**
- `kubectl auth can-i list pods --as=system:serviceaccount:dev:viewer -n dev` → yes
- `kubectl auth can-i list services --as=system:serviceaccount:dev:viewer -n dev` → no
- Pod en `prod` con `svcviewer` puede listar servicios, no pods

---
