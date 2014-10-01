---
layout: blog
tittle: Utilización de OpenStack (1)
menu:
  - Unidad 2
---
## Práctica: Trabajar con instancias Windows

El objetivo de esta práctica es realizar todo el proceso para crear una instancia con una distribución Windows.

1. Crea una instancia a partir de un sistema operativo Windows, teniendo en cuanta los siguientes aspectos:

	* Tienes que abrir el puerto 3389 en el grupo de seguridad para poder acceder usando el protocolo RDP.
	* Cuando trabajamos con instancias Windows no tiene sentido utilizar claves ssh para acceder a ellas.
	* La primera vez que accedes a la instancia te pedirá que cambies la contraseña del Administrador.

	Por medio de un cliente RDP, accede a la instancia que has creado utilizando la IP flotante que le has asignado.
