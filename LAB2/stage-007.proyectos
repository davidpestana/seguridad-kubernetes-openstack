## 🔧 Fase 7 – Redes compartidas y aislamiento entre proyectos

**🌟 Objetivo:**
Explorar cómo se comportan las redes cuando se comparten entre proyectos o se mantienen aisladas, y cómo se controla ese comportamiento desde Neutron.

**📜 Descripción:**
En esta fase analizaremos el aislamiento de red por proyecto, y cómo crear redes que puedan o no ser utilizadas por otros. Se crearán redes en distintos proyectos y se observará su visibilidad y acceso desde usuarios con diferentes permisos.

---

### 🧭 Acciones desde CLI

1. **Autenticarse como usuario del proyecto A:**

   ```bash
   source openrc-devops-a.sh
   ```

2. **Crear una red privada y verificar su visibilidad:**

   ```bash
   openstack network create red-privada-a
   openstack network list
   ```

3. **Crear una red compartida:** (como admin)

   ```bash
   openstack network create red-compartida --share
   ```

4. **Autenticarse como usuario del proyecto B y listar redes:**

   ```bash
   source openrc-devops-b.sh
   openstack network list
   ```

---

### 💥 Acciones desde Horizon

* Crear una red en un proyecto sin marcar "Shared"
* Cambiar de proyecto en Horizon: la red no debe ser visible
* Crear una red como admin y marcarla como **Compartida**
* Verificar que los proyectos secundarios pueden usarla al crear instancias

---

### ⚖️ Comparativa redes privadas vs compartidas

| Comportamiento                | Red privada         | Red compartida              |
| ----------------------------- | ------------------- | --------------------------- |
| Visible desde otros proyectos | ❌                   | ✅                           |
| Utilizable para instancias    | Solo en su proyecto | En todos los proyectos      |
| Creación desde Horizon        | ✅                   | ✅                           |
| Requiere permisos de admin    | ❌                   | ✅ (para marcar como shared) |

---

### 🧹 Retos para el alumno

1. Crear una red en `devops-a` y comprobar que `devops-b` no la ve
2. Intentar lanzar una instancia en `devops-b` usando una red de otro proyecto
3. Crear una red compartida como admin y verificar su uso en ambos proyectos

---

### ✅ Validaciones esperadas

* Las redes privadas no aparecen fuera de su proyecto
* Las redes compartidas aparecen en todos los proyectos
* Se puede crear una instancia en `devops-b` usando la red compartida

---

## 🤩 Reto final (funcional): Interconexión de proyectos mediante una red común

**Escenario:**
Los equipos `devops-a` y `devops-b` necesitan compartir una red para conectividad entre sus instancias, manteniendo el resto de recursos aislados.

**Objetivo:**
➡️ Crear una red compartida, desplegar una instancia por proyecto, y validar que ambas pueden comunicarse internamente a través de esa red.

### 📌 Pistas

* Crea la red como `--share` y sin gateway externo
* Usa una subred en rango privado (ej: `10.99.0.0/24`)
* Verifica conectividad con `ping` entre las dos instancias
* Comprueba que no tienen acceso exterior si no usas Floating IP ni router
