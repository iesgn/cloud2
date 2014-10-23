---
layout: blog
tittle: Utilización de OpenStack (4). APIS
menu:
  - Unidad 5
---
##Creación de una instancia con la aplicación nova.

El objetivo de esta demostración es la creación de una instancia usando el clinete nova. Partimo de un escenario donde tenemos instalado el cliente (paquete *python-covaclient*) y vamos a ver los distintos pasos que tenemos que realizar:

1. Lo primero es bajarnos el fichero de credenciales y ejecutarlo en nuestro ordenador:

	![credenciales](img/demo1_1.png)

	Y la instalamoes en nuestro ordenador:
		
		$ source values.sh
		Please enter your Password:

2. A continuación vamos a crear un par de claves ssh que me permitan acceder a la instancia:
		
		$ nova keypair-add clave_acceso > ~/.ssh/clave-acceso.pem
		$ chmod 600 ~/.ssh/clave-acceso.pem

3. Ahora vamos acrear un grupo seguridad (cortafuegos) donde vamos a crear una regla que nos permita el acceso por ssh.

		$ nova secgroup-create gr_seguridad "Reglas de seguridad"
		+--------------------------------------+--------------+---------------------+
		| Id                                   | Name         | Description         |
		+--------------------------------------+--------------+---------------------+
		| 72479d3c-6dd3-45f6-9335-050793190209 | gr_seguridad | Reglas de seguridad |
		+--------------------------------------+--------------+---------------------+
		$ nova secgroup-add-rule gr_seguridad tcp 22 22 0.0.0.0/0
		+-------------+-----------+---------+-----------+--------------+
		| IP Protocol | From Port | To Port | IP Range  | Source Group |
		+-------------+-----------+---------+-----------+--------------+
		| tcp         | 22        | 22      | 0.0.0.0/0 |              |
		+-------------+-----------+---------+-----------+--------------+

4. Antés de crear la instancia vamos a ver las imágnes, flavor y redes que tenemos disponibles.

