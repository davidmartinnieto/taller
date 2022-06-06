# Validación de los pre-requisitos

## Tabla de contenidos

* [Objetivos](#Objetivos)
* [Guía](#Guía)
* [Su entorno de laboratorio](#Su-entorno-de-laboratorio)
* [Paso 1 - Acceso al entorno](#Paso-1---Acceso-al-entorno)
* [Paso 2 - Trabajar los laboratorios](#Paso-2---Trabajar-los-laboratorios)
* [Paso 3 - Laboratorios de desafío](#Paso-3---Laboratorios-de-desafío)

# Objetivos

- Comprender la topología de laboratorio y cómo acceder al entorno.
- Comprender cómo trabajar los ejercicios del taller
- Comprender los laboratorios de desafío

# Guía

## Su entorno de laboratorio

En este laboratorio se trabaja en un entorno de laboratorio preconfigurado. El controller-node debeis instalarlo en vuestro equipo local, y además debéis tener acceo a los siguientes nodos administrados:
| Role                 | Inventory name |
| ---------------------| ---------------|
| Managed Host 1       | nodo1.devsecops.local |
| Managed Host 2       | nodo2.devsecops.local |
| Managed Host 3       | nodo3.devsecops.local |

## Paso 1 - Revisión del controller-node.

Verificar que Ansible se ha instalado correctamente:

```bash
    [root@ansible ~]# ansible --version
    ansible [core 2.12.3]
    [...]
```
> **Nota**
>
> Ansible mantiene la administración de la configuración simple. Ansible no requiere base de datos ni demonios en ejecución y puede ejecutarse fácilmente en un portátil. En los hosts administrados no necesita ningún agente en ejecución.

## Paso 2 - Trabajar con el entorno de laboratorio

El acceso a las máquinas debe hacerse con el usuario `ansible` mediante intercambio de claves público/privadas. En el directorio de trabajo `ansible-files/ssl/` está desplegada la clave pública y privada a utilizar.

Se debe poder iniciar sesión en los nodos administrados mediante ssh:

```bash
ssh ansible@nodo1.devsecops.local -i ~/ansible/ansible-files/ssl/id_rsa_taller_ansible
```

## Paso 3 - Laboratorios de desafío

En los laboratorios existe una sección de "Laboratorio de desafío". Estos laboratorios están destinados a darle una pequeña tarea para resolver utilizando lo que ha aprendido hasta ahora. La solución de la tarea se muestra debajo de un signo de advertencia.

----
**Navegación**
<br>
[Próximo Ejercicio](../1.2-adhoc/README.es.md)

