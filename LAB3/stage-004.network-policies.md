### Fase 4: Aplicación de Network Policies

**🎯 Objetivos:**
- Instalar un CNI compatible con políticas de red (Calico)
- Aplicar NetworkPolicies para restringir tráfico entre pods
- Validar reglas de acceso este-oeste

---

**📝 Descripción:**
Hasta ahora, los pods podían comunicarse libremente entre namespaces. En esta fase se instalará Calico como CNI para habilitar el soporte de NetworkPolicies. Se aplicarán políticas para controlar el tráfico entre servicios y comprobar su efectividad mediante pruebas prácticas.

---

**🔧 Pasos detallados (ejercicio guiado):**

✅ 1. Eliminar el clúster actual y recrearlo sin CNI:
```bash
kind delete cluster --name seg-cluster
kind create cluster --name seg-cluster --config kind-config.yaml --retain
```

✅ 2. Instalar Calico como CNI:
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

✅ 3. Confirmar instalación:
```bash
kubectl get pods -n calico-system
kubectl get nodes -o wide
```

✅ 4. Crear una política por defecto que niegue todo el tráfico:
Archivo: `default-deny.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: dev
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```
```bash
kubectl apply -f default-deny.yaml
```

✅ 5. Verificar que App B ya no puede acceder a App A:
```bash
kubectl exec -n prod app-b -- curl --max-time 3 http://app-a.dev.svc.cluster.local
```
❌ Debería fallar (timeout).

✅ 6. Crear una política que permita solo acceso desde `prod`:
Archivo: `allow-from-prod.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-prod-to-dev
  namespace: dev
spec:
  podSelector:
    matchLabels:
      app: app-a
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: prod
```
```bash
kubectl label namespace prod name=prod
kubectl apply -f allow-from-prod.yaml
```

✅ 7. Probar de nuevo el acceso desde App B:
```bash
kubectl exec -n prod app-b -- curl http://app-a.dev.svc.cluster.local
```
✅ Debería funcionar.

---

**🎯 Reto para el alumno:**
- Crear una política que permita el acceso a App C **solo desde dentro del namespace `test`**
- Etiquetar correctamente el namespace y los pods
- Verificar que `app-b` en `prod` **no** puede acceder a App C
- Verificar que otro pod en `test` **sí** puede

Entrega esperada: manifiesto YAML `networkpolicy-appc.yaml`, comandos y pruebas de acceso.

---

**✅ Validaciones:**
- `kubectl get networkpolicy -A` → se ven las políticas aplicadas
- Acceso denegado por defecto
- Acceso concedido solo si lo permite la política
- Etiquetas en namespaces correctamente aplicadas

---
