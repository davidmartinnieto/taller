# Taller - Inventarios, credenciales y comandos ad hoc

## Tabla de contenidos

* [Objetivos](#Objetivos)
* [Guía](#Guía)
* [Examinar un inventario](#Examinar-un-inventario)
* [Credenciales de máquina](#Credenciales-de-máquina)
* [Configurar credenciales de máquina](#Examinar-las-credenciales-de-la-máquina)
* [Ejecutar comandos Ad Hoc](#Ejecutar-comandos-ad-hoc)
* [Laboratorios de desafío: Comandos Ad Hoc](#Laboratorios-de-desafío-Comandos-Ad-Hoc)

# Objetivos

Explore y comprenda el entorno de laboratorio.  Este ejercicio cubrirá
- Ubicación y comprensión:
  - Ansible Tower [**Inventarios**](https://docs.ansible.com/ansible-tower/latest/html/userguide/inventories.html)
  - Ansible Tower [**Credenciales**](https://docs.ansible.com/ansible-tower/latest/html/userguide/credentials.html)
- Ejecución de comandos ad hoc a través de la interfaz de usuario web de Ansible Tower


# Guía

## Examinar un inventario

Lo primero que necesitamos es un inventario de sus hosts administrados. Esto es el equivalente de un archivo de inventario en Ansible Engine. Hay mucho más (como inventarios dinámicos), pero comencemos con lo básico.

Se ha provisionado un inventario, **[Academia] Taller Inventario**. Haga clic en el **[Academia] Taller Inventario** y, a continuación, haga clic en el botón **Hosts**

Es necesario definir la información del inventario utlizado durante el taller: `~/ansible/taller-ansible/inventario`

```bash
$ cat ~/ansible/taller-ansible/inventario/hosts
[all:vars]
ansible_user=ansible

ansible_port=22

[web]
nodo1 ansible_host=10.243.64.42
nodo2 ansible_host=10.243.64.43
nodo3 ansible_host=10.243.64.44
```

## Examinar las credenciales de la máquina

Ahora examinaremos las credenciales para acceder a nuestros hosts administrados desde Tower.  Como parte del proceso de provisionamiento para este Taller Ansible, ya se ha configurado las **[Academia] Credenciales Taller Ansible**.

En el menú **RESOURCES**, elija **Credentials**. Ahora haga clic en la **[Academia] Credenciales Taller Ansible**.

Tenga en cuenta la siguiente información:

<table>
  <tr>
    <th>Parámetro</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>Credential Type</td>
    <td><code>Machine</code>- Las credenciales de máquina definen el acceso a la escalación de privilegios ssh y de nivel de usuario para los playbooks. Se utilizan al enviar trabajos para ejecutar playbook en un host remoto.</td>
  </tr>
  <tr>
    <td>username</td>
    <td><code>ansible</code>- que coincide con nuestro nombre de usuario de inventario Ansible de la línea de comandos para los otros nodos de Linux</td>
  </tr>
  <tr>
    <td>SSH PRIVATE KEY</td>
    <td><code>ENCRYPTED</code> - tomar nota de que en realidad no se puede examinar la clave privada SSH una vez que alguien la entrega a Ansible Tower</td>
  </tr>
</table>

## Ejecutar comandos Ad Hoc

También es posible ejecutar comandos ad hoc desde Ansible Tower.

  - En la interfaz de usuario web vaya a **RESOURCES → Inventories → [Academia] Taller Inventario**

  - Haga clic en el botón **HOSTS** para cambiar a la vista de hosts y seleccione los tres hosts marcando las casillas a la izquierda de las entradas del host.

  - Haga clic en **RUN COMMANDS**. En la siguiente pantalla debe especificar el comando ad hoc:


  <table>
    <tr>
      <th>Parámetro</th>
      <th>Valor</th>
    </tr>
    <tr>
      <td>MODULE</td>
      <td>ping</td>
    </tr>
    <tr>
      <td>MACHINE CREDENTIAL</td>
      <td>Workshop Credentials</td>
    </tr>
  </table>

  - Haga clic en **LAUNCH** y observe la salida.


<hr>

El módulo **ping** no necesita opciones. Para otros módulos, debe proporcionar el comando para que se ejecute como argumento. Pruebe el módulo **command** para buscar el ID de usuario del usuario en ejecución mediante un comando ad hoc.


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
      <td>id</td>
    </tr>
  </table>

> **Consejo**
>
> Después de elegir el módulo que se va a ejecutar, Tower proporcionará un enlace a la página de documentos del módulo al hacer clic en el signo de interrogación situado junto a "Arguments". Esto es útil, pruébalo.

<hr>

¿Qué tal si tratamos de obtener información secreta del sistema? Intente imprimir */etc/shadow*.


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
    <td>cat /etc/shadow</td>
  </tr>
</table>


> **Advertencia**
>
> **¡Espere un error!**

Oops, el último no salió bien, todo rojo.

Vuelva a ejecutar el último comando ad hoc, pero esta vez marque la casilla **ENABLE PRIVILEGE ESCALATION**.

Como ves, esta vez funcionó. Para las tareas que tienen que ejecutarse como root, debe escalar los privilegios. Esto es lo mismo que el **become: yes** utilizado en sus Playbooks de Ansible.

## Laboratorio de Desafíos: Comandos Ad Hoc

Bien, un pequeño desafío: Ejecuta un comando ad hoc para asegurarte de que el paquete "tmux" está instalado en todos los hosts. Si no está seguro, consulte la documentación a través de la interfaz de usuario web como se muestra arriba o ejecutando `[ansible@tower ]$ ansible-doc yum` en el host de control de Tower.


> **Advertencia**
>
> **Solución a continuación\!**

<table>
  <tr>
    <th>Parámetro</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>yum</td>
    <td>command</td>
  </tr>
  <tr>
    <td>ARGUMENTS</td>
    <td>name=tmux</td>
  </tr>
  <tr>
    <td>ENABLE PRIVILEGE ESCALATION</td>
    <td>✓</td>
  </tr>
</table>

> **Consejo**
>
> La salida amarilla del comando indica que Ansible ha hecho algo (aquí se necesita instalar el paquete). Si ejecuta el comando ad hoc por segunda vez, la salida será verde e informará de que el paquete ya estaba instalado. Así que el amarillo en Ansible no significa "warning"... ;-).


----
**Navegación**
<br>
[Ejercicio anterior](../2.1-intro/README.es.md) - [Próximo Ejercicio](../2.3-projects/README.es.md)

