## Manual básico de Vagrant

Este documento resume los comandos y conceptos esenciales para manejar entornos locales con Vagrant en el contexto del curso.

### ¿Qué es Vagrant?

Vagrant es una herramienta que permite crear y gestionar entornos virtualizados de forma reproducible mediante archivos de configuración llamados `Vagrantfile`.

### Comandos básicos

Inicializar un nuevo entorno (solo si no existe un Vagrantfile):

```bash
vagrant init
```

Arrancar la máquina virtual:

```bash
vagrant up
```

Acceder a la máquina virtual:

```bash
vagrant ssh
```

Detener la máquina virtual (sin eliminarla):

```bash
vagrant halt
```

Reiniciar la máquina virtual:

```bash
vagrant reload
```

Destruir la máquina virtual y liberar espacio:

```bash
vagrant destroy
```

### Ver el estado de la VM

```bash
vagrant status
```

### Estructura típica del proyecto

```
proyecto/
├── Vagrantfile
├── .vagrant/           # Carpeta interna de estado
└── devstack/           # Carpeta usada por DevStack
```

### Limpieza de plugins rotos (si da error al iniciar)

```bash
vagrant plugin expunge --reinstall
```

### Consejos

* Siempre entrar con `vagrant ssh`, nunca accediendo por VirtualBox directamente.
* Apagar con `halt` conserva el estado; `destroy` lo elimina todo.
* El `Vagrantfile` define CPU, RAM, red, y provisiones automáticas.
* Usa puertos altos (por encima de 1024) si haces port forwarding y tienes conflictos.
