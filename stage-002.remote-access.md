## Stage 002 - Acceso remoto a la máquina "tower" desde VS Code

### 📚 Objetivos de la fase

Establecer acceso remoto desde el editor Visual Studio Code hacia la máquina `tower`, permitiendo trabajar de forma cómoda con los playbooks, inventarios y roles alojados en la VM.

---

### 🔧 Pasos a realizar

1. **Instalar extensión necesaria en VS Code**

* Abrir VS Code
* Ir a la sección de extensiones
* Buscar e instalar: `Remote - SSH`

2. **Obtener IP privada de la VM `tower`**

```bash
vagrant ssh tower -c "ip -4 a | grep inet"
```

> Anotar la IP asignada en la red privada (normalmente `192.168.x.x`)

3. **Agregar acceso en función del sistema operativo del host**

#### 🖥️ Windows

* Abrir PowerShell o Git Bash
* Crear archivo `C:\Users\<usuario>\.ssh\config` (si no existe)
* Añadir:

```
Host tower-lab
  HostName <IP_de_tower>
  User vagrant
  IdentityFile C:/Users/<usuario>/.vagrant.d/insecure_private_key
```

#### 💡 WSL2

* Abrir WSL2 (Ubuntu) y editar `~/.ssh/config`:

```
Host tower-lab
  HostName <IP_de_tower>
  User vagrant
  IdentityFile ~/.vagrant.d/insecure_private_key
```

#### 💻 Linux

* Editar `~/.ssh/config`:

```
Host tower-lab
  HostName <IP_de_tower>
  User vagrant
  IdentityFile ~/.vagrant.d/insecure_private_key
```

#### 🧥 macOS

* Editar `~/.ssh/config`:

```
Host tower-lab
  HostName <IP_de_tower>
  User vagrant
  IdentityFile ~/.vagrant.d/insecure_private_key
```

4. **Conectar desde VS Code**

* Abrir paleta de comandos: `Ctrl+Shift+P`
* Ejecutar: `Remote-SSH: Connect to Host...`
* Seleccionar: `tower-lab`

5. **Montar el workspace del laboratorio**
   Una vez conectado:

* Abrir carpeta: `/home/vagrant/labs`
* Guardar como workspace si se desea reutilizar fácilmente

---

### ✅ Validaciones necesarias

* [ ] El archivo `~/.ssh/config` contiene la entrada "tower-lab" con la IP correcta
* [ ] Se puede hacer `ssh tower-lab` desde el terminal del host sin contraseña
* [ ] La extensión Remote - SSH está instalada en VS Code
* [ ] Se puede conectar a `tower` desde VS Code sin errores
* [ ] Se abre la carpeta `~/labs` desde el entorno remoto
* [ ] Se puede editar un archivo de prueba dentro de `~/labs`
* [ ] La terminal integrada en VS Code abre correctamente una sesión sobre `tower`

---

### 📂 Estructura de archivos esperada

```
~/.ssh/config         # Contiene definición "tower-lab"
~/labs/
├── inventory/
├── playbooks/
└── roles/
```

---

### 🔗 Documentación oficial relacionada

* [https://code.visualstudio.com/docs/remote/ssh](https://code.visualstudio.com/docs/remote/ssh)
* [https://developer.hashicorp.com/vagrant/docs](https://developer.hashicorp.com/vagrant/docs)
* [https://www.ssh.com/academy/ssh/config](https://www.ssh.com/academy/ssh/config)
