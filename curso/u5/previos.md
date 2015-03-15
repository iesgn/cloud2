---
layout: blog
tittle: Pasos previos
menu:
  - Unidades
---

## Instalación de los clientes

Cada componente de OpenStack tiene su propio cliente de línea de comandos con toda las funcionalidades. Existe un cliente "unificado" (nova) que permite utilizar las funcionalidades básicas de los componentes principales.

Instalamos en el equipo el cliente de línea de comandos de OpenStack (con este método instalamos la última versión):

		# apt-get install python-pip
		# pip install python-novaclient
		# pip install python-neutronclient

## Descargar el fichero de credenciales

Una vez que tenemos instalados los clientes, tenemos que autentificarnos en el sistema, para ello, tenemos que bajar el fichero de credenciales y ejecutarlo en nuestro ordenador:

	![credenciales](img/demo1_1.png)

	Y la instalamoes en nuestro ordenador:
		
		$ source values.sh
		Please enter your Password: