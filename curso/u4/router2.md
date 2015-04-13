---
layout: blog
tittle: Creación de un router y conectarlo entre dos redes internas
menu:
  - Unidades
---

En esta demostración vamos a crear una nueva red, una nueva subred y un nuevo router. El router estará conectado a las dos redes internas. El esquema de red sería el siguiente:

![red](img/red4.png)

Tenemos que tener en cuenta los siguientes aspectos:

* Al crear el nuevo router, no lo vamos a conectar a la red interna.
* Hay que modificar las características de la subred de la primera red 00000061-net para que el servidor dhcp mande una ruta a las instancias conectadas a la red para que sean capaces de conectarse a las instancias de la segunda red.
* A las instancias que esten conectadas a la segunda red no se le podrán asociar ip flotantes, ya que el mecanismo NAT en un router no conectado a la red externa no se puede realizar.

Vamos a ello:


1. En el apartado **Administrador de redes** hemos creado una red nueva con un subred con direccionamiento 192.168.0.0/24 y un nuevo router. Al crear el router no he indicado la **Red externa**, por lo que no estará conectada a esa red.

	![red](img/net8_1.png)

2. Vamos a conectar el nuevo router a nuestra primera red, para ello vamos a crear un nuevo puerto en la subred.

	![red](img/net9.png)

	Tenemos que indicar los siguientes datos:

	* Nombre del puerto.
	* En que red vamos 
