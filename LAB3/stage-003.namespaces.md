### Fase 3: Aislamiento con Namespaces

**🎯 Objetivos:**
- Comprender cómo usar namespaces para aislar recursos
- Aplicar buenas prácticas de organización por entorno
- Comprobar que los recursos no son accesibles entre namespaces por defecto

---

**📝 Descripción:**
Los namespaces permiten dividir lógicamente los recursos dentro del clúster. En esta fase se verá cómo organizar los despliegues por entorno (por ejemplo, `dev`, `prod`, `test`) y cómo verificar el aislamiento básico. Aunque por defecto no existe una barrera de red entre namespaces, sí se aísla a nivel de recursos y visibilidad.

---

**🔧 Pasos detallados (ejercicio guiado):**

✅ 1. Listar los pods en todos los namespaces (como admin):
```bash
kubectl get pods -A
```

✅ 2. Desde un pod en `dev`, intentar listar pods en `prod`:
Archivo: `dev-isolated-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: isolated
  namespace: dev
spec:
  containers:
  - name: cli
    image: bitnami/kubectl
    command: ["sleep", "infinity"]
```
```bash
kubectl apply -f dev-isolated-pod.yaml
kubectl exec -n dev isolated -- kubectl get pods -n prod
```
✅ Debería fallar por falta de permisos si se usa una ServiceAccount sin roles cruzados.

✅ 3. Comprobar visibilidad DNS entre namespaces:
```bash
kubectl exec -n dev isolated -- nslookup app-a.dev.svc.cluster.local
kubectl exec -n dev isolated -- nslookup app-a.prod.svc.cluster.local
```
✅ Ambos deberían resolverse, lo que demuestra que **no hay aislamiento de red aún**.

✅ 4. Reforzar la organización por entorno:
- Usar etiquetas comunes por entorno (`env=dev`, `env=prod`)
- Aplicar políticas RBAC que solo afecten dentro del namespace

---

**🎯 Reto para el alumno:**
- Crea un nuevo namespace llamado `secure`
- Despliega una app dummy (`nginx`) con la etiqueta `env=secure`
- Crea un Role y RoleBinding que impidan que otras ServiceAccounts externas puedan leer sus pods
- Verifica desde otro namespace que no puede acceder a los pods de `secure`

Entrega esperada: manifiestos YAML y pruebas realizadas con `kubectl exec`

---

**✅ Validaciones:**
- `kubectl get pods -n secure` desde otro namespace → acceso denegado
- `nslookup` de servicios entre namespaces → funciona (sin políticas de red)
- Etiquetas aplicadas correctamente (`kubectl get pods --show-labels`)

---
