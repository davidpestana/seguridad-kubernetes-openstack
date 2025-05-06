### Fase 6: Evaluación final y despliegue conjunto

**🎯 Objetivos:**
- Reforzar conocimientos con un despliegue integrado
- Evaluar la seguridad de red, acceso y visibilidad de secretos
- Validar la correcta aplicación de RBAC, NetworkPolicies y namespaces

---

**📝 Descripción:**
En esta fase se realiza una práctica final que combina todos los conceptos anteriores: despliegue de una aplicación en varios namespaces, aplicación de RBAC y NetworkPolicies, y uso controlado de Secrets. Servirá como revisión general de los aprendizajes del laboratorio.

---

**🔧 Pasos sugeridos:**

✅ 1. Crear un namespace `app-final` con label `env=final`

✅ 2. Crear un Secret `jwt-secret` en ese namespace:
```bash
kubectl create secret generic jwt-secret \
  --from-literal=token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9 \
  -n app-final
```

✅ 3. Desplegar dos pods:
- `frontend`: expuesto vía NodePort 30090, consume el secret como env
- `backend`: solo accesible desde el frontend (NetworkPolicy)

✅ 4. Aplicar las siguientes políticas:
- RBAC: solo `frontend` puede listar pods en su namespace
- NetworkPolicy: solo `frontend` puede acceder al `backend`
- Secret: solo el `frontend` accede al valor de `jwt-secret`

✅ 5. Validar conectividad:
- `curl` desde `frontend` a `backend` → ✅
- `curl` desde otro namespace → ❌

✅ 6. Validar acceso a secretos y pods:
- Desde el `frontend`, ver JWT token y listar pods → ✅
- Desde un pod sin permisos, intentar listar → ❌

---

**🎯 Reto de cierre:**
- Añade un tercer pod `intruder` en namespace `test`
- Intenta acceder al backend y al secreto de `app-final`
- Verifica que el acceso es denegado en todos los casos
- Prepara una tabla resumen de qué pod puede hacer qué acción

---

**✅ Validaciones:**
- El flujo `frontend → backend` funciona únicamente en el namespace `app-final`
- Accesos no autorizados desde `test` son bloqueados por políticas de red y RBAC
- Secret `jwt-secret` es visible solo en el contenedor correcto
- Todos los manifiestos están documentados y justificados

---
