# Taller - Proyectos y job templates

## Tabla de contenidos

* [Objetivos](#Objetivos)
* [Guía](#Guía)
* [Configurar el repositorio Git](#configurar-el-repositorio-Git)
* [Crear el proyecto](#Crear-el-proyecto)
* [Crear una job templates y ejecutar un trabajo](#Crear-una-plantilla-de-trabajo-y-ejecutar-un-trabajo)
* [Laboratorio de Desafío: Compruebe el resultado](#Laboratorio-de-Desafío-Compruebe-el-resultado)
* [¿Qué hay de un poco de práctica?](#¿Qué-hay-de-un-poco-de-práctica)

# Objetivos

Un proyecto de ansible Tower **Project** es una colección lógica de Playbooks de Ansible. Puede administrar sus playbooks colocándolos en un sistema de administración de código fuente (SCM) compatible con Tower, incluidos Git, Subversion y Mercurial.

Este ejercicio cubre
- Comprender y utilizar un proyecto de Ansible Tower.
- Uso de Playbooks de Ansible guardados en un repositorio Git.
- Creación y uso de una job templates ansible.

# Guía

## Configurar el repositorio Git

Para esta demostración usaremos playbooks almacenados en otro repositorio Git:

**https://gitlab.devsecops.local/grupo-publico/taller-awx-examples**

Ya se ha implementado un Playbook para instalar el servidor web Apache en el directorio **rhel/apache**, `apache_install.yml`:

```yaml
---
- name: Apache server installed
  hosts: all

  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest

  - name: latest firewalld version installed
    yum:
      name: firewalld
      state: latest

  - name: firewalld enabled and running
    service:
      name: firewalld
      enabled: true
      state: started

  - name: firewalld permits http service
    firewalld:
      service: http
      permanent: true
      state: enabled
      immediate: yes

  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
```

> **Consejo**
>
> Note la diferencia con otros Playbooks que podría haber escrito! Lo más importante es que no hay `become` y `hosts`" se establece como `all`.

Para configurar y utilizar este repositorio como un **Sistema de Control de Código Fuente (SCM)** en Tower, debe crear un **Project** que utilice el repositorio

## Crear el proyecto

  - Ir a **RESOURCES → Projects** en la vista de menú lateral haga clic en el botón verde **+**. Rellene el formulario

  <table>
    <tr>
      <th>Parámetro</th>
      <th>Valor</th>
    </tr>
    <tr>
      <td>NAME</td>
      <td>Workshop Project</td>
    </tr>
    <tr>
      <td>ORGANIZATION</td>
      <td>Default</td>
    </tr>
    <tr>
      <td>SCM TYPE</td>
      <td>Git</td>
    </tr>
  </table>

Ahora necesita la dirección URL para acceder al repositorio. Vaya al repositorio de Github mencionado anteriormente, elija el botón verde **Clone or download** a la derecha, haga clic en **Use https** y copie la URL HTTPS.


 Introduzca la dirección URL en la configuración del proyecto:

 <table>
   <tr>
     <th>Parámetro</th>
     <th>Valor</th>
   </tr>
   <tr>
     <td>SCM URL</td>
     <td><code>https://gitlab.devsecops.local/grupo-publico/taller-awx-examples.git</code></td>
   </tr>
   <tr>
     <td>SCM UPDATE OPTIONS</td>
     <td>Marque las tres primeras casillas para obtener siempre una copia nueva del repositorio y actualizar el repositorio al iniciar un trabajo</td>
   </tr>
 </table>


- Clic en **SAVE**

El nuevo proyecto se sincronizará automáticamente después de la creación. Pero también puede hacerlo manualmente: Sincronice el proyecto de nuevo con el repositorio De Git yendo a la vista **Projects** y haciendo clic en la flecha circular **Sync Project**  a la derecha del proyecto.

Después de iniciar el trabajo de sincronización, vaya a la vista **Jobs**: hay un nuevo trabajo para la actualización del repositorio de Git.

## Crear un job template y ejecutar un job

Una job template es una definición y un conjunto de parámetros para ejecutar un trabajo Ansible. Los job templates son útiles para ejecutar el mismo trabajo muchas veces. Entonces, antes de ejecutar un **Job** Ansible desde Tower, debe crear un **Job Template** que reúna:

- **Inventario**: ¿En qué hosts debe ejecutarse el trabajo?

- **Credenciales** ¿Qué credenciales se necesitan para iniciar sesión en los hosts?

- **Proyecto**: ¿Dónde está el Playbook?

- **¿Qué** Playbook para usar?

Bien, vamos a hacer eso: Vaya a la vista **Templates**, haga clic en el ![plus](images/green_plus.png) y elija **Job Template**.

<table>
  <tr>
    <th>Parámetro</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>NAME</td>
    <td>Install Apache</td>
  </tr>
  <tr>
    <td>JOB TYPE</td>
    <td>Run</td>
  </tr>
  <tr>
    <td>INVENTORY</td>
    <td>Workshop Inventory</td>
  </tr>
  <tr>
    <td>PROJECT</td>
    <td>Workshop Project</td>
  </tr>
  <tr>
    <td>PLAYBOOK</td>
    <td><code>rhel/apache/apache_install.yml</code></td>
  </tr>    
  <tr>
    <td>CREDENTIAL</td>
    <td>Workshop Credentials</td>
  </tr>
  <tr>
    <td>LIMIT</td>
    <td>web</td>
  </tr>    
  <tr>
    <td>OPTIONS</td>
    <td>tasks need to run as root so check **Enable privilege escalation**</td>
  </tr>           
</table>

- Clic **SAVE**

Puede iniciar el trabajo haciendo clic directamente en el botón **LAUNCH** o haciendo clic en el cohete en la vista general de Job Templates. Después de iniciar el job template, se redireccionará automáticamente a la descripción general del trabajo, donde puede seguir la ejecución del playbook en tiempo real:

![job exection](images/job_overview.png)

Puesto que esto puede tomar algún tiempo, eche un vistazo más de cerca a todos los detalles proporcionados:

- La ejecución real del playbook queda registrada aquí. Esto hace que sea fácil analizar la ejecución del trabajo más adelante.
Se muestran todos los detalles de la job templates como inventario, proyecto, credenciales y playbook.

- También se registra el tiempo de ejecución con la hora de inicio y finalización, lo que le da una idea de cuánto tiempo toma realmente una ejecución de trabajo.

- En el lado derecho, se muestra la salida de la ejecución del playbook. Haga clic en un nodo debajo de una tarea y vea que se proporciona información detallada para cada tarea de cada nodo.

Una vez que el trabajo haya terminado, vaya a la vista principal **Jobs**: todos los jobs se listan aquí, debería ver directamente antes de que el Playbook se ejecute una actualización desde el repo. Esta es la actualización de Git que configuramos para el **Project** en lanzamiento\!

## Laboratorio de Desafío: Compruebe el resultado

Es hora de un pequeño desafío:

  - Utilice un comando ad hoc en ambos hosts para asegurarse de que Apache se ha instalado y se está ejecutando.

Ya has pasado por todos los pasos necesarios, así que prueba esto por ti mismo.

> **Consejo**
>
> ¿Qué pasa con 'systemctl status httpd'?

> **Advertencia**
>
> **Solución a continuación**

- Ir a **Inventories** → **Workshop Inventory**

- En la vista **HOSTS** seleccione todos los hosts y haga clic en **RUN COMMANDS**

- Rellene lo siguiente:

<table>
  <tr>
    <th>Parámetro</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>MODULE</td>
    <td>command</td>
  </tr>
  <tr>
    <td>ARGUMENTS</td>
    <td>systemctl status httpd</td>
  </tr>
  <tr>
    <td>MACHINE CREDENTIALS</td>
    <td>Credenciales Taller Ansible</td>
  </tr>   
</table>

- Clic en **LAUNCH**

## ¿Qué hay de un poco de práctica?

Aquí hay una lista de tareas:

> **Advertencia**
>
> Por favor, asegúrese de terminar estos pasos, ya que el siguiente capítulo depende de él!

- Crear un nuevo inventario llamado `Webserver` que sólo contenga `nodo1`.

- Copiar el job template `Install Apache` que hemos utilizado.

- Cambiar el nombre a `Pregunta Install Apache`

- Cambiar la configuración **INVENTORY** del proyecto para que solicite el inventario a la hora de lanzar el job. 

- **GUARDAR**

- Inicie la plantilla `Pregunta Install Apache`.

- Ahora solicitará el inventario que se utilizará, elija el inventario del `Webserver` y haga clic en  **LAUNCH**

- Espere hasta que el trabajo haya terminado y asegúrese de que se ejecuta sólo en `node1`

> **Consejo**
>
> El trabajo no cambió nada porque Apache ya estaba instalado en la última versión.


----
**Navegación**
<br>
[Ejercicio anterior](../2.2-cred/README.es.md) - [Next Exercise](../2.4-surveys/README.es.md)

