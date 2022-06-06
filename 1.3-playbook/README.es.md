# Escribir el primer Playbook

## Tabla de contenidos

- [Objetivos](#objective)
- [Guía](#guide)
  - [Paso 1 - Fundamentos del los playbooks](#step-1---playbook-basics)
  - [Paso 2 - Creación de una estructura de directorios y un archivo para su Playbook](#step-2---creating-a-directory-structure-and-file-for-your-playbook)
  - [Paso 3 - Ejecución del Playbook](#step-3---running-the-playbook)
  - [Paso 4 - Extiende tu Playbook: Iniciar &amp; Habilitar Apache](#step-4---extend-your-playbook-start--enable-apache)
  - [Paso 5 - Extiende tu Playbook: Crear un web.html](#step-5---extend-your-playbook-create-an-indexhtml)
  - [Paso 6 - Práctica: Aplicar a múltiples hosts](#step-6---practice-apply-to-multiple-host)

# Objetivos

Este ejercicio cubre el uso de Ansible para crear dos servidores web Apache en Red Hat Enterprise Linux. Este ejercicio cubre los siguientes fundamentos de Ansible:

- Comprender los parámetros de los módulos de ansible
- Comprender y utilizar los siguientes módulos
  - [módulo yum](https://docs.ansible.com/ansible/latest/modules/yum_module.html)
  - [módulo service](https://docs.ansible.com/ansible/latest/modules/service_module.html)
  - [módulo copy](https://docs.ansible.com/ansible/latest/modules/copy_module.html)
- Comprender la [Idempotencia](https://en.wikipedia.org/wiki/Idempotence) y cómo los módulos Ansible pueden ser idempotentes

# Guía

Aunque los comandos ad hoc de Ansible son útiles para operaciones sencillas, no son adecuados para escenarios complejos de administración de configuraciones o orquestación. Para estos casos los *playbooks* son el camino a seguir.

Los Playbooks son archivos que describen las configuraciones deseadas o los pasos para implementar en hosts administrados. Los playbooks pueden cambiar tareas administrativas largas y complejas en rutinas fácilmente repetibles con resultados predecibles y exitosos.

Un playbook es donde puedes tomar algunos de esos comandos ad hoc que acabas de ejecutar y ponerlos en un conjunto repetible de *plays* y *tasks*.

Un playbook puede tener varios plays y un play puede tener una o varias tareas. En una tarea se llama a un *módulo*, como los módulos del capítulo anterior. El objetivo de un *play* es mapear un grupo de hosts.  El objetivo de una *tarea* es implementar módulos en esos hosts.

> **Consejo**
>
> Analogía: Cuando los módulos Ansible son las herramientas en su taller, el inventario son los materiales y los Playbooks las instrucciones.

## Paso 1 - Fundamentos de los playbooks

Los Playbooks son archivos de texto escritos en formato YAML y por lo tanto necesitan:

  - empezar con tres guiones (`---`)

  - Indentación adecuada utilizando espacios y **no** tabuladores\!


Conceptos importantes:

  - **hosts**: los hosts administrados sobre los que realizar las tareas.

  - **tasks**: las operaciones a realizar invocando módulos Ansible con los argumentos necesarios.

  - **become**: escalado de privilegios en playbooks. Igual que el flag --become en comandos ad-hoc.

> **Advertencia**
>
> El orden de los contenidos dentro de un Playbook es importante, ya que Ansible ejecuta plays y tareas en el mismo orden que se han escrito.

Un Playbook debe ser **idempotente**, por lo que si un Playbook se ejecuta una vez para poner los hosts en el estado correcto,  debe ser seguro ejecutarlo más veces sin hacer cambios en los nodos administrados.

> **Consejo**
>
> La mayoría de los módulos Ansible son idempotentes, por lo que es relativamente fácil asegurarse de que esto sea cierto.


## Paso 2 - Creación de una estructura de directorios y un archivo para su Playbook

Es hora de crear tu primer Playbook. En este laboratorio se crea un playbook para configurar un servidor web Apache en tres pasos:

  1. Instalar el paquete httpd

  2. Habilitar/iniciar el servicio httpd

  3. Copiar un archivo web.html en cada host del grupo de inventario web.

Este Playbook se asegura de que el paquete que contiene el servidor web Apache esté instalado en `nodo1`.

Hay una documento de [mejores prácticas](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html) con la estructura de directorio recomendada para los playbooks. Le recomendamos encarecidamente que lea y entienda estas prácticas a medida que desarrolla sus habilidades en Ansible.  Dicho esto, nuestro playbook de hoy es muy básico y la creación de una estructura compleja sólo confundirá las cosas.

En su lugar, vamos a crear una estructura de directorios muy simple para nuestro playbook, y añadir sólo un par de archivos a él.

Accede al directorio de trabajo y crea un archivo llamado `apache.yml` con el siguiente contenido.

```yaml
---
- name: Apache server installed
  hosts: nodo1
  become: yes
```

Esto muestra uno de los puntos fuertes de Ansible: la sintaxis del playbook es fácil de leer y entender. En este Playbook:

  - Se da un nombre para el play a través de `name:`.

  - El host donde se ejecuta el playbook se define a través de `hosts:`.

  - Habilitamos el escalado de privilegios mediante `become:`.

> **Nota**
>
> Obviamente, necesita usar escalado de privilegios para instalar un paquete o ejecutar cualquier otra tarea que requiera permisos de usuario root. Esto se hace en el Playbook con `become: yes`.

Ahora que hemos definido el play, vamos a añadir una tarea para hacer algo. Agregaremos una tarea en la que yum se asegurará de que el paquete Apache esté instalado en la última versión. Modifique el archivo para que tenga el aspecto como el siguiente:

```yaml
---
- name: Apache server installed
  hosts: nodo1
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
```

> **Consejo**
>
> Dado que los playbooks están escritos en YAML, la identación de las líneas y palabras clave es crucial. Asegúrese de alinear verticalmente el *t* en `task` con el *b* en `become`. Una vez que esté más familiarizado con Ansible, asegúrese de tomarse un tiempo y estudiar un poco la [Sintaxis YAML](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html).

En las líneas añadidas:

  - Comenzamos la parte de tareas con la palabra clave `tasks:`.

  - Se nombra una tarea y se hace referencia al módulo de la tarea. Aquí utiliza el módulo `yum`.

  - Se añaden parámetros para el módulo:

    - `name:` para identificar el nombre del paquete
    - `state:` para definir el estado deseado del paquete

> **Consejo**
>
> Los parámetros del módulo son individuales para cada módulo. En caso de duda, búsquelos de nuevo con `ansible-doc`.

Guarde su playbook y salga de su editor.

## Paso 3 - Ejecución del Playbook

Los Playbooks ansibles se ejecutan mediante el comando `ansible-playbook` en el nodo de control. Antes de ejecutar un nuevo Playbook, es una buena idea comprobar si hay errores de sintaxis:

```bash
[ansible@ansible ansible-files]$ ansible-playbook --syntax-check apache.yml
```

Ahora deberías estar listo para ejecutar tu playbook:

```
[ansible@ansible ansible-files]$ ansible-playbook apache.yml
```

La salida no debe informar de ningún error, sino proporcionar una visión general de las tareas ejecutadas y un resumen de reproducción que resume lo que se ha hecho. También hay una tarea llamada "Gathering Facts" listada allí: esta es una tarea integrada que se ejecuta automáticamente al principio de cada play. Recopila información sobre los nodos administrados. Los ejercicios posteriores cubrirán esto con más detalle.

```
[ansible@ansible ansible-files]$ ssh ansible@nodo1 -i ~/ansible/ssl/id_rsa_taller_ansible
Last login: Wed May 15 14:03:45 2019 from 44.55.66.77
Managed by Ansible
```

Utilice el comando `rpm -qi httpd` para verificar que httpd está instalado:

```
[ansible@nodo1 ~]$ rpm -qi httpd
Name        : httpd
Version     : 2.4.6
[...]
```

¡Cierre la sesión de `nodo1` con el comando `exit` para que vuelva al host de control y verifique el paquete instalado con un comando Ad hoc de Ansible\!

```bash
[ansible@ansible ansible-files]$ ansible nodo1 -m command -a 'rpm -qi httpd'
```

Ejecute el Playbook una segunda vez y compare la salida: la salida ha cambiado de "changed" a "ok", y el color cambió de amarillo a verde. También el "PLAY RECAP" es diferente ahora. Esto hace que sea fácil detectar lo que Ansible realmente hizo.

## Paso 4 - Extiende tu Playbook: Iniciar y Habilitar Apache

La siguiente parte del Ansible Playbook se asegura de que la aplicación Apache esté habilitada e iniciada en `nodo1`.

En el host de control, edite el archivo `~/ansible-files/apache.yml` para agregar una segunda tarea mediante el módulo `service`. El Playbook ahora debería tener este aspecto:


```yaml
---
- name: Apache server installed
  hosts: nodo1
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
```

Una vez más: lo que hacen estas líneas es fácil de entender:

  - se crea una segunda tarea y se nombra

  - se especifica un módulo (`service`)

  - se suministran los parámetros del módulo

Por lo tanto, con la segunda tarea nos aseguramos de que el servidor Apache se está ejecutando en la máquina de destino. Ejecuta tu Playbook:

```bash
[ansible@ansible ansible-files]$ ansible-playbook apache.yml
```

Tenga en cuenta la salida ahora: Algunas tareas se muestran como "ok" en verde y una se muestra como "changed" en amarillo.
  - Utilice un comando Ansible ad hoc de nuevo para asegurarse de que Apache se ha habilitado e iniciado, por ejemplo, con: `systemctl status httpd`.

  - Ejecutar el Playbook una segunda vez para acostumbrarse a los cambios en la salida del comando.

## Paso 5 - Extiende tu Playbook: Crear un web.html

Compruebe que las tareas se han ejecutado correctamente y Apache está aceptando conexiones: realice una solicitud HTTP utilizando el módulo `uri` de Ansible en un comando ad hoc desde el nodo de control. Asegúrese de reemplazar el valor de la **\<dirección IP\>** con la dirección IP del nodo del inventario.

> **Advertencia**
>
>**¡Espere una gran cantidad de líneas rojas y un estado 403\!**

```bash
[ansible@ansible ansible-files]$ ansible localhost -m uri -a "url=http://<IP>"
```

Hay un montón de líneas rojas y un error: Mientras no haya al menos un archivo `web.html` para ser servido por Apache, lanzará un feo estado "HTTP Error 403: Forbidden" y Ansible informará de un error.

Entonces, ¿por qué no usar Ansible para implementar un simple archivo `web.html`? En el host de control ansible, crear el directorio `files` para contener los recursos de archivo en `~/ansible-files/`':

```bash
[ansible@ansible ansible-files]$ mkdir files
```

A continuación, cree el archivo `~/ansible-files/files/web.html` en el nodo de control:

```html
<body>
<h1>Apache is running OK</h1>
</body>
```

Ya ha utilizado el módulo `copy` de Ansible para escribir el texto proporcionado en la línea de comandos en un archivo. Ahora usarás el módulo de tu Playbook para copiar un archivo:

En el nodo de control, mientras el usuario del student edita el archivo `/ansible-files/apache.yml` y agrega una nueva tarea utilizando el módulo `copy`. Ahora debería tener este aspecto:

```yaml
---
- name: Apache server installed
  hosts: nodo1
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
  - name: copy web.html
    copy:
      src: web.html
      dest: /var/www/html/index.html
```

Te estás acostumbrando a la sintaxis de Playbook, así que ¿qué pasa? La nueva tarea utiliza el módulo `copy` y define las opciones de origen y destino para la operación de copia como parámetros.

Ejecuta tu Playbook extendido:

```bash
[ansible@ansible ansible-files]$ ansible-playbook apache.yml
```
  - Echar un buen vistazo a la salida

  - Ejecute el comando ad hoc usando el módulo "uri" de más arriba de nuevo para probar Apache: El comando ahora debe devolver una línea verde amigable "status: 200", entre otra información.

## Paso 6 - Práctica: Aplicar a múltiples hosts

Esto fue agradable, pero el verdadero poder de Ansible es aplicar el mismo conjunto de tareas de forma fiable a muchos hosts.

  - Entonces, ¿qué pasa con cambiar el Playbook apache.yml para que se ejecute en `nodo1` **y** `nodo2` **y** `nodo3`?

Como puede recordar, el inventario enumera todos los nodos como miembros del grupo `web`:

```ini
[web]
nodo1 ansible_host=10.243.64.42
nodo2 ansible_host=10.243.64.43
nodo3 ansible_host=10.243.64.44
```

> **Consejo**
>
> Las direcciones IP que se muestran aquí son solo ejemplos, sus nodos tendrán diferentes direcciones IP.

Cambie el Playbook para que apunte al grupo "web":

```yaml
---
- name: Apache server installed
  hosts: web
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
  - name: copy web.html
    copy:
      src: web.html
      dest: /var/www/html/index.html
```

Ahora ejecute el Playbook:

```bash
[ansible@ansible ansible-files]$ ansible-playbook apache.yml
```

Por último, compruebe si Apache se está ejecutando en ambos servidores. Identifique primero las direcciones IP de los nodos de su inventario y, a continuación, utilícelas cada una en el comando ad hoc con el módulo uri como ya hicimos con el `nodo1` anterior. Toda la salida debe ser verde.

> **Consejo**
>
> Una forma alternativa de verificar que Apache se está ejecutando en ambos servidores es utilizar el comando `ansible nodo2,nodo3 -m uri -a "url=http://localhost/"`.


----
**Navegación**
<br>
[Ejercicio anterior](../1.2-adhoc/README.es.md) - [Próximo Ejercicio](../1.4-variables/README.es.md)

