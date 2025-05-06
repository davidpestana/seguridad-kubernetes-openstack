### Fase 5: Uso seguro de Secrets

**🎯 Objetivos:**
- Comprender el funcionamiento de los Kubernetes Secrets
- Aplicar buenas prácticas en su uso y acceso
- Validar restricciones de visibilidad y permisos

---

**📝 Descripción:**
Los Secrets permiten guardar información sensible como contraseñas, tokens o claves TLS. En esta fase el alumno verá cómo se crean, cómo se consumen en pods y qué riesgos implica su mala gestión. Se reforzará el principio de mínimo privilegio mediante RBAC y validaciones.

---

**🔧 Pasos detallados (ejercicio guiado):**

✅ 1. Crear un Secret en el namespace `dev`:
```bash
kubectl create secret generic db-password \
  --from-literal=password=SuperSecreto123 \
  -n dev
```

✅ 2. Consultar el contenido del Secret (como admin):
```bash
kubectl get secret db-password -n dev -o yaml
```
📌 Verás el valor en Base64. Para decodificar:
```bash
echo "U3VwZXJTZWNyZXRvMTIz" | base64 -d
```

✅ 3. Crear un pod que consuma el Secret como variable de entorno:
Archivo: `pod-with-secret.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-test
  namespace: dev
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "echo PASSWORD=$DB_PASSWORD && sleep 3600"]
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-password
          key: password
```
```bash
kubectl apply -f pod-with-secret.yaml
kubectl logs -n dev secret-test
```
✅ Verifica que la variable se imprime correctamente.

✅ 4. Crear un Role en `dev` que **no** puede acceder a Secrets:
Archivo: `dev-no-secrets-role.yaml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: deny-secrets
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

✅ 5. Asociarlo a una nueva ServiceAccount:
```bash
kubectl create serviceaccount limited -n dev
kubectl apply -f dev-no-secrets-role.yaml
```
Archivo: `dev-no-secrets-binding.yaml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: bind-deny-secrets
  namespace: dev
subjects:
- kind: ServiceAccount
  name: limited
  namespace: dev
roleRef:
  kind: Role
  name: deny-secrets
  apiGroup: rbac.authorization.k8s.io
```
```bash
kubectl apply -f dev-no-secrets-binding.yaml
```

✅ 6. Lanzar un pod con esa ServiceAccount y probar acceso a Secrets:
Archivo: `test-denied.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: denied
  namespace: dev
spec:
  serviceAccountName: limited
  containers:
  - name: test
    image: bitnami/kubectl
    command: ["sleep", "infinity"]
```
```bash
kubectl apply -f test-denied.yaml
kubectl exec -n dev denied -- kubectl get secrets
```
❌ Debería dar error por falta de permisos.

---

**🎯 Reto para el alumno:**
- Crea un Secret en `prod` llamado `api-token`
- Define un pod que lo monte como archivo en `/secrets/token.txt`
- Crea una ServiceAccount con permisos mínimos (no puede leer Secrets)
- Comprueba que el pod accede al archivo pero no puede listar Secrets vía API

Entrega esperada: YAML del Secret, pod, ServiceAccount, Role y RoleBinding, más pruebas en terminal.

---

**✅ Validaciones:**
- El Secret se monta correctamente y es accesible desde el contenedor
- La ServiceAccount no puede listar secrets (`kubectl get secrets`) si no tiene permisos
- Solo el contenedor accede al valor, no hay exposición adicional

---
