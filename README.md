# 🛡️ Laboratorio de Ciberseguridad en Kubernetes y OpenStack

Este repositorio contiene un entorno práctico y modular diseñado para acompañar el curso **"Ciberseguridad en Kubernetes y OpenStack" (25h)**. Su objetivo es ofrecer una plataforma reproducible y realista donde los participantes puedan desplegar, interconectar y asegurar entornos basados en OpenStack y Kubernetes desde cero, utilizando herramientas DevOps modernas como Vagrant y Ansible.

---

## 🎯 Objetivos del laboratorio

* Desplegar un entorno completo de laboratorio en máquina local usando Vagrant.
* Automatizar la configuración de entornos con Ansible desde una máquina de control (`tower`).
* Implementar DevStack (OpenStack) y KIND (Kubernetes) como plataformas base.
* Validar principios de ciberseguridad en entornos cloud e híbridos.
* Facilitar el acceso remoto desde VS Code para una experiencia de desarrollo fluida.

---

## 🔍 Motivación

Las organizaciones modernas demandan profesionales capaces de gestionar entornos híbridos donde la seguridad sea un componente nativo, no un añadido posterior. Este laboratorio:

* Refuerza los conceptos fundamentales del **Esquema Nacional de Seguridad (ENS)**.
* Permite simular escenarios reales de ataque y defensa en plataformas IaaS y CaaS.
* Une la teoría con la práctica, enfocándose en la automatización segura de entornos cloud.

---

## 🧱 Arquitectura del entorno

La topología básica incluye tres máquinas virtuales creadas por Vagrant:

* **`tower`**: nodo de control Ansible con conectividad SSH a las demás VMs.
* **`openstack`**: nodo para desplegar DevStack (OpenStack).
* **`k8s`**: nodo para desplegar Kubernetes con KIND.

```
Host
├── tower (Ansible Control Node)
│   ├── Acceso remoto por VS Code
│   └── Ejecuta playbooks sobre:
│       ├── openstack → DevStack (OpenStack)
│       └── k8s → KIND (Kubernetes)
```

Las máquinas se conectan entre sí por red privada (`192.168.56.0/24`) y se accede desde el host tanto por terminal como desde Visual Studio Code usando `Remote - SSH`.

---

## 📁 Estructura del repositorio

```
.
├── Vagrantfile                    # Define las VMs del laboratorio
├── manual.vagrant.md             # Guía de uso de Vagrant
├── stage-001.ansible-tower-control.md  # Preparación del nodo de control Ansible
├── stage-002.remote-access.md    # Configuración del acceso remoto con VS Code
├── stage-003.devstack.md         # Despliegue de OpenStack (DevStack)
├── stage-004.kind.md             # Despliegue de Kubernetes con KIND
├── temario.md                    # Contenido detallado del curso de 25h
└── .gitignore
```

---

## 🔧 Requisitos previos

* Sistema operativo con soporte para VirtualBox y Vagrant (Linux, macOS, Windows+WSL)
* Vagrant instalado (`>=2.2`)
* VirtualBox instalado (`>=6.0`)
* Visual Studio Code (opcional, pero recomendado)
* Extensión `Remote - SSH` en VS Code para acceder cómodamente a la VM `tower`

---

## 🧪 Proceso de uso

1. **Levantar las VMs del laboratorio**:

```bash
vagrant up
```

2. **Acceder a la torre de control**:

```bash
vagrant ssh tower
```

3. **Ejecutar los stages definidos** para configurar:

   * Ansible en `tower`
   * DevStack en `openstack`
   * KIND en `k8s`
   * Configuración remota desde VS Code

4. **Usar `ansible-playbook`** para automatizar despliegues y pruebas de seguridad.

---

## 📚 Más información

* [Documentación oficial de Vagrant](https://developer.hashicorp.com/vagrant)
* [Documentación oficial de Ansible](https://docs.ansible.com/)
* [DevStack](https://docs.openstack.org/devstack/latest/)
* [KIND](https://kind.sigs.k8s.io/)

---

## 🧑‍🏫 Créditos y uso educativo

Este laboratorio ha sido diseñado con fines **educativos** como parte del curso especializado en **Ciberseguridad para entornos Cloud Híbridos**. Está optimizado para formación guiada, pero también puede adaptarse para autoaprendizaje.

---
