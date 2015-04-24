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

### Creación de la red y el router

En el apartado **Administrador de redes** hemos creado una red nueva con un subred con direccionamiento 192.168.0.0/24 y un nuevo router. Al crear el router no he indicado la **Red externa**, por lo que no estará conectada a esa red. La red la hemos conectado al nuevo router.

	![red](img/net8_1.png)

Utilizando *neutron* sería:

		neutron net-create mi_red
		neutron subnet-create mi_subred 192.168.0.0/24
		neutron router-create mi_router
		neutron router-interface-add mi_router mi_subred

### Añadir una nueva interfaz al router

Vamos a conectar el nuevo router a nuestra primera red, para ello vamos a crear un nuevo puerto en la subred.

	![red](img/net9.png)

	Al crear un puerto tenemos que indicar los siguientes datos:

	* Nombre del puerto.
	* En que red vamos y en que subred vamos a crear el puerto.
	* Dirección del puerto

	Actualmente tenemos un puerto que no está conectado a ningún dispositivo.

Utilizando *neutron* tendráimos que ejecutar:

	neutron port-create 00000061-net --fixed-ip ip_address=10.0.15.69
	Created a new port:
	+-----------------------+-----------------------------------------------------------------------------------+
	| Field                 | Value                                                                             |
	+-----------------------+-----------------------------------------------------------------------------------+
	| admin_state_up        | True                                                                              |
	| allowed_address_pairs |                                                                                   |
	| device_id             |                                                                                   |
	| device_owner          |                                                                                   |
	| fixed_ips             | {"subnet_id": "4751dc7e-6b54-4284-ba02-6c1a44deb076", "ip_address": "10.0.15.69"} |
	| id                    | 5138b3e0-6a88-4f79-8804-01d7c50c020f                                              |
	| mac_address           | fa:16:3e:2a:92:99                                                                 |
	| name                  |                                                                                   |
	| network_id            | d5d686b5-32fb-4e45-8809-98df3ee5ef3e                                              |
	| security_groups       | 6b8e211b-51d3-482c-9865-6b6e0bb3c94b                                              |
	| status                | DOWN                                                                              |
	| tenant_id             | 44f5cb63ad34481aab5cc9c2809e4a76                                                  |
	+-----------------------+-----------------------------------------------------------------------------------+

La conexión del router (mi_router) al puerto que acabamos de crear **no se puede realizar desde la aplicación web Cirrusflex**, por lo tanto la única forma de hacerlo es a través del cliente *neutron*:

		neutron router-interface-add mi_router port=5138b3e0-6a88-4f79-8804-01d7c50c020f

En la instrucción anterior hemos utilizado el id del puerto que acabamos de crear.