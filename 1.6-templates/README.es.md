# Plantillas

## Tabla de contenidos

* [Objetivos](#Objetivos)
* [Guía](#Guía)
* [Paso 1 - Uso de plantillas en Playbooks](#Paso-1---Uso-de-plantillas-en-Playbooks)
* [Paso 2 - Laboratorios de desafío](#Paso-2---Laboratorios-de-desafío)

# Objetivos

Este ejercicio cubrirá las plantillas de Jinja2. Ansible utiliza plantillas de Jinja2 para modificar archivos antes de que se distribuyan a los hosts administrados. Jinja2 es uno de los motores de plantillas más utilizados para Python (<http://jinja.pocoo.org/>).

# Guía

## Paso 1 - Uso de plantillas en Playbooks

Cuando se ha creado una plantilla para un archivo, se puede implementar en los hosts administrados mediante el módulo `template`, que admite la transferencia de un archivo local desde el nodo de control a los hosts administrados.

Como ejemplo de uso de plantillas, cambiará el archivo motd para que contenga datos específicos del host.

En primer lugar, cree el directorio `templates` que contendrá los recursos de plantilla en `~/ansible-files/`:

```bash
[ansible@ansible ansible-files]$ mkdir templates
```
A continuación, en el directorio `~/ansible-files/templates/`, cree el archivo de plantilla `motd-facts.j2`:

<!-- {% raw %} -->
```html+jinja
Welcome to {{ ansible_hostname }}.
{{ ansible_distribution }} {{ ansible_distribution_version}}
deployed on {{ ansible_architecture }} architecture.
```
<!-- {% endraw %} -->

El archivo de plantilla contiene el texto básico que se copiará más adelante. También contiene variables que se reemplazarán en las máquinas de destino individualmente.

A continuación, necesitamos un playbook para usar esta plantilla. En el directorio `~/ansible-files/`, cree el Playbook `motd-facts.yml`:

```yaml
---
- name: Fill motd file with host data
  hosts: nodo1
  become: true
  tasks:
    - template:
        src: motd-facts.j2
        dest: /etc/motd
        owner: root
        group: root
        mode: 0644
```
Usted ha hecho esto un par de veces por ahora:

  - Entender lo que hace el Playbook.

  - Ejecutar el Playbook `motd-facts.yml`.

  - Inicie sesión en nodo1 a través de SSH y compruebe el mensaje del contenido del día.
  - Cierre la sesión del nodo1.

Debería ver cómo Ansible reemplaza las variables con los facts que descubrió del sistema.

## Paso 2 - Laboratorios de desafío

Agregue una línea a la plantilla para listar el kernel actual del nodo administrado.

  - Encuentra un fact que contenga la versión del kernel usando los comandos que aprendiste en el capítulo "Ansible Facts".

> **Consejo**
>
> Hacer un `grep -i` para el kernel

  - Modificar la plantilla para utilizar el fact que encontró.

  - Ejecute el Playbook de nuevo.


> **Advertencia**
>
> **Solución a continuación\!**


  - Encuentra el fact:
```bash
[ansible@ansible ansible-files]$ ansible nodo1 -m setup|grep -i kernel
       "ansible_kernel": "3.10.0-693.el7.x86_64",
```

  - Modificar la plantilla `motd-facts.j2`:
<!-- {% raw %} -->
```html+jinja
Welcome to {{ ansible_hostname }}.
{{ ansible_distribution }} {{ ansible_distribution_version}}
deployed on {{ ansible_architecture }} architecture
running kernel {{ ansible_kernel }}.
```
<!-- {% endraw %} -->

  - Ejecute el playbook.
```
[ansible@ansible ~]$ ansible-playbook motd-facts.yml
```
  - Verifique el nuevo mensaje a través del login por SSH al `nodo1`.
```
[ansible@ansible ~]$ ssh nodo1
Welcome to nodo1.
RedHat 8.1
deployed on x86_64 architecture
running kernel 4.18.0-147.8.1.el8_1.x86_64.
```

----
**Navegación**
<br>
[Ejercicio anterior](../1.5-handlers/README.es.md) - [Próximo Ejercicio](../1.7-role/README.es.md)

