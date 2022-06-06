# Taller de automatización con Ansible & Ansible AWX/Tower.

Ansible es un motor de automatización de IT simple pero potente para el despliegue de aplicaciones, la gestión de la configuración y la orquestación de las operaciones.

En la primera sección de este laboratorio se aprenderá a utilizar el motor de Ansible para la automatización desde conceptos básicos hasta algunos coneceptos más avanzados.

En la segunda sección se introduce el uso de Ansible a través de Ansible Tower/AWX, un orquestador que añade funcionalidades al uso de Ansible.

## Presentaciones

El taller sigue ejercicios que son autoexplicativos y guiados. Todos los conceptos se van explicando cuando se introducen.

Adicionalmente, se adjuntan dos presentaciones como material de apoyo:

- [Ansible RHEL Automation](decks/ansible_rhel.pdf)

- [Ansible Best Practices](decks/ansible_best_practices.pdf)

## Planificación

El tiempo necesario para realizar el taller depende en gran medida del número de participantes y lo familizarizados que esten con Linux en general.

De forma aproximada, durante la mañana deberia realizarse tanto la introducción como la sección 1, dejando para la tarde los ejercicios y demos relativos a Ansible Tower.

## Sección 0 - Introducción
- Presentacion [Ansible Best Practices](decks/ansible_best_practices.pdf) 
- A los teclados:

  - Instalación de una distribución de Linux. ( Subsistema WSL2, VirtualBox...)
  
  - Instalación cliente VPN para tener conectividad con el lab de IBMCloud.
  
  - Instalación de Ansible Engine:
    > ** Nota **
    >
    > El comando instalará la versión core de Ansible que incluye los módulos necesarios para hacer el laboratorio.
    ```bash
    sudo apt install ansible -y
    ```

  - Clonar el repositorio del taller:
    ```bash
     export GIT_SSL_NO_VERIFY=true
     git clone https://gitlab.devsecops.local/grupo-publico/taller-ansible.git ~/ansible
    ```

  - Crear vuestra propia branch para escribir sobre ella las soluciones
    ```bash
    cd ~/ansible
    git checkout -b 'solutions.<<Nombre>>'
    ```

  - Acceder al directorio de trabajo:
    ```bash
    cd ~/ansible/ansible-files/
    ```
### Entorno:

Para el laboratorio se han desplgado tres máquinas en IBMCloud las cuales se administraran mediante ansible:

| Nodos | Hostname | IP |
|-------|----------|----|
| nodo1 | nodo1.devsecops.local| 10.243.64.42|
| nodo2 | nodo2.devsecops.local| 10.243.64.43|
| nodo3 | nodo3.devsecops.local| 10.243.64.44|

El acceso debe realizarse con la clave privada ssl adjuntada al repositorio y el usuario ansible.


## Sección 1 - Ejercicios de Ansible Engine

 - [Ejercicio 1.1 - Validación de los pre-requisitos](1.1-setup/README.es.md)
 - [Ejercicio 1.2 - Ejecución de comandos Ad-hoc](1.2-adhoc/README.es.md)
 - [Ejercicio 1.3 - Escribir el primer Playbook](1.3-playbook/README.es.md)
 - [Ejercicio 1.4 - Uso de Variables](1.4-variables/README.es.md)
 - [Ejercicio 1.5 - Condicionales, controladores y bucles](1.5-handlers/README.es.md)
 - [Ejercicio 1.6 - Plantillas](1.6-templates/README.es.md)
 - [Ejercicio 1.7 - Roles](1.7-role/README.es.md)

## Sección 2 - Ejercicios de Ansible Tower

 - [Ejercicio 2.1 - Introducción a Tower](2.1-intro/README.es.md)
 - [Ejercicio 2.2 - Inventarios, credenciales y comandos ad hoc](2.2-cred/README.es.md)
 - [Ejercicio 2.3 - Proyectos y plantillas de trabajo](2.3-projects/README.es.md)
 - [Ejercicio 2.4 - Encuestas](2.4-surveys/README.es.md)
 - [Ejercicio 2.5 - Control de acceso basado en roles](2.5-rbac/README.es.md)
 - [Ejercicio 2.6 - Flujos de trabajo](2.6-workflows/README.es.md)
 - [Ejercicio 2.7 - Conclusión](2.7-wrap/README.es.md)

##  Ejercicios Complementarios
Se han incluido ejercicios complementarios al taller. Echa un vistazo a los ejercicios si quieres más contenido.
 - [Ejercicios Complementarios](supplemental)

## Información adicional

 - [Ansible Getting Started](http://docs.ansible.com/ansible/latest/intro_getting_started.html)

