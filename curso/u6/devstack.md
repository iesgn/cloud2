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