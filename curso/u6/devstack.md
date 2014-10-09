---
layout: blog
tittle: Cloud Computing en Educación y en la Empresa
menu:
  - Unidad 6
---

###Devstack

DevStack es un conjunto de scripts en bash que nos permiten instalar OpenStack de forma automática.

Vamos a realizar la instalación de OpenStack IceHouse utilizando el entrono de prueba DevStack, sólo será posible el acceso desde la misma máquina donde hemos realizado la instalación. Se puede instalar en una máquina física o en una virtual, sin embargo hay que tener en cuenta que en este último caso se usará [qemu](http://es.wikipedia.org/wiki/QEMU) para la emulación de las máquinas virtuales con lo que se perderá rendimiento.

####Requisitos mínimos

* Equipo necesario: RAM 2Gb y procesador VT-x/AMD-v
* Ubuntu 14.04 instalado, con los paquetes actualizados.
* Git instalado

		$ sudo apt-get upgrade
		$ sudo apt-get install git

####Instalación

1. Tenemos que clonar el repositorio git de Devstack, la rama de la versión icehouse:

		$ git clone -b stable/icehouse https://github.com/openstack-dev/devstack.git
		$ cd devstack 
2. A continuación tenemos que configurar la instalación de OpenStack, para ello bajamos el archivo [local.conf](https://raw.githubusercontent.com/iesgn/cloud2/gh-pages/curso/u6/local.conf) y lo guardamos en el directorio devstack:

		~/devstack$ wget https://raw.githubusercontent.com/iesgn/cloud2/gh-pages/curso/u6/local.conf

3. Y ya podemos comenzar la instalación:

		~/devstack$	./stack.sh

4. Una vez terminada la instalación, para acceder a la aplicación web Horizon:

	* Accedemos a la URL *http://localhost*.
    * Usuario de prueba: **demo** con contraseña **devstack**.
    * Usuario de administración: **admin** con contraseña **devstack**.
    * El usuario **demo** debe trabajar en el proyecto *"demo"*, no en uno que se llama *"invisible_to_admin"*.

5. Estamos trabajando en un entorno de pruebas, por lo tento si terminamos de trabajar con Openstack y apagamos el ordenador, la próxima vez que queramos trabajar con él los servicios no estarán arrancados. Por lo tanto si queremos seguir trabajando con la sesión anterior, tendremos que ejecutar la siguiente instrucción:

		$ cd devstack
		~/devstack$ ./rejoin-stack.sh

	Si comprobamos que no funciona bien, tendremos que volver a instalar devstack (aunque esta segunda vez la instalación será mucho más rápida) aunque perderemos todos los cambios realizados (instancias, imágenes, grupos de seguridad,...):

		$ cd devstack
		~/devstack$ ./stack.sh

####Máquina virtualbox con devstack

Si el proceso de instalación da algún problema, podemos probar OpenStack usando una máquina virtual de VirtualBox con devtack instalada. Es una máquina virtual con un interfaz de red tipo NAT, por lo tanto sólo se puede trabajar con openstack desde la misma máquina.

* Bájate el fichero [ova]().
* Importa la máquina a virtualbox. La máquina está configurada con 3GB de RAM; modifica este parámetro según tus necesidades.
* Para acceder usa el usuario y contraseña: *usuario*.
* Lo primero que tienes que hacer es acceder al directorio *devstack* y ejecutar el script de instalación (stack.sh).
* Puede ser una buena idea que suspendas la máquina (Guardar el estado) y así poder seguir trabajando en otro momento.



