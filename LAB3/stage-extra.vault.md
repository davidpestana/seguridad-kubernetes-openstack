### Fase 7: Introducción a Vault como gestor externo de secretos

**🎯 Objetivos:**
- Comprender las limitaciones de Kubernetes Secrets nativos
- Introducir Vault como gestor externo seguro de secretos
- Integrar Vault en el clúster para recuperar valores secretos desde pods

---

**📝 Descripción:**
En esta fase se muestra una introducción práctica a HashiCorp Vault como gestor externo de secretos. Se desplegará Vault en modo desarrollo dentro del clúster y se configurará una integración básica con Kubernetes para que un pod recupere un secreto gestionado externamente.

⚠️ Esta integración se usará con un despliegue simplificado para fines educativos (modo dev), no recomendado en producción.

---

**🔧 Pasos detallados (ejercicio guiado):**

✅ 1. Desplegar Vault en modo dev:
```bash
kubectl create ns vault
helm repo add hashicorp https://helm.releases.hashicorp.com
helm install vault hashicorp/vault \
  --namespace vault \
  --set "server.dev.enabled=true"
```

✅ 2. Esperar a que el pod de Vault esté listo:
```bash
kubectl get pods -n vault
```

✅ 3. Crear un secreto en Vault:
```bash
kubectl exec -n vault vault-0 -- \
  vault kv put secret/api token=SuperToken123
```

✅ 4. Crear un pod que acceda al secreto desde Vault:
Archivo: `vault-pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: vault-test
  namespace: dev
spec:
  containers:
  - name: vault-client
    image: curlimages/curl
    command: ["sh", "-c"]
    args:
      - |
        curl -s \
          --header "X-Vault-Token: root" \
          http://vault.vault.svc.cluster.local:8200/v1/secret/data/api | jq
```
```bash
kubectl apply -f vault-pod.yaml
kubectl logs -n dev vault-test
```
✅ Deberías ver el contenido del secreto recuperado desde Vault.

---

**🎯 Reto para el alumno:**
- Cambia el valor del token en Vault a `NuevoToken456`
- Repite la petición desde el pod y valida el nuevo valor
- Explica por qué esta solución es más segura y flexible que los Secrets nativos

Entrega esperada: captura de logs y reflexión escrita sobre ventajas comparativas

---

**✅ Validaciones:**
- Vault funciona en modo dev dentro del clúster
- El pod puede recuperar un secreto vía HTTP API
- El secreto puede actualizarse dinámicamente sin recrear el pod
- Comprensión básica de la arquitectura Vault + K8s

---
