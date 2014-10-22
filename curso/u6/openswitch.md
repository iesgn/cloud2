---
layout: blog
tittle: Administración de OpenStack
menu:
  - Unidad 6
---

## OpenSwitch

En las unidades anteriores se han visto dos elementos fundamentales para
configurar la infraestructura como servicio, esto es máquinas virtuales y
almacenamiento, pero falta un último elemento: la <a
href="http://en.wikipedia.org/wiki/Network_virtualization">virtualización de
redes</a>. La virtualización de redes consiste en desacoplar las redes virtuales
del hardware de red para lograr una mejor integración de los entornos virtuales.

El componente de OpenStack que se encarga de gestionar la virtualización de
redes es neutron y es un componente que se incluyó en OpenStack Folsom con el
nombre de quantum. OpenStack neutron está sometido a un importante desarrollo y
va incluyendo paulatinamente diferentes tecnologías de red como "backends"
mediante <a
href="https://wiki.openstack.org/wiki/Neutron#Using_Neutron">plugins</a>

En nuestro caso, vamos a utilizar Open vSwitch como la tecnología por debajo de
neutron encargada de la virtualización de la red.

**Nota:** Aunque en OpenStack Havana está recomendado utilizar Open vSwitch
mediante el nuevo plugin *Modular Layer 2* (ML2), hemos utilizado directamente
el plugin de Open vSwitch.

## Open vSwitch

<a href="http://openvswitch.org/">Open vSwitch (OVS)</a> es una implementación
de software libre de un switch virtual distribuido multicapa.

Las máquinas GNU/Linux incluyen ya un software para la utilización de switches
L2, software conocido como "linux bridge", incluido en el kérnel linux y
manejado con las herramientas del espacio de usuario "bridge-utils" y en
particular con la instrucción *brctl*. OpenvSwitch es un desarrollo nuevo e
independiente que se incluyó en la versión 3.3 del kérnel linux y que
proporciona mayor funcionalidad para entornos de virtualización de redes, como
muy bien se explica en <a
href="http://git.openvswitch.org/cgi-bin/gitweb.cgi?p=openvswitch;a=blob_plain;f=WHY-OVS;hb=HEAD">Why
OpenvSwitch?</a>.

## Características necesarias

En un entorno con varios nodos de computación como tenemos en OpenStack,
precisamos de algunas características de la red como las siguientes:

- Máquinas que se ejecutan en el mismo nodo de computación, pero que estén en
  diferente red local.
- Máquinas que se ejecutan en diferentes nodos de computación, pero que
  virtualmente estén en la misma red local.
- Cuando sea preciso, un servidor DHCP por cada subred que no interfiera con los
  de otras subredes
- Reglas de cortafuegos definidas por máquina, no por red.
- Tráfico seguro entre nodos de computación
- Conexión de las máquinas virtuales con el exterior.

## Tipo de despliegue

Dependiendo del uso que vaya a tener nuestro cloud, optaremos por alguno de los
tipos de despliegue que soporta neutron, en particular debemos saber si:

* Todas las instancias de todos los proyectos estarán dentro de una misma
VLAN, lo que OpenStack denomina <a
href="http://docs.openstack.org/grizzly/openstack-network/admin/content/app_demo_flat.html">Single
Flat Network</a>.
* Cada proyecto podrá definir las redes privadas que quiera, pero compartirán el
o los routers de acceso al exterior, lo que OpenStack denomina <a
href="http://docs.openstack.org/grizzly/openstack-network/admin/content/app_demo_single_router.html">
Provider Router with Private Networks</a>
* Cada proyecto podrá definir las redes privadas y routers que quiera, de forma
independiente al resto de proyectos, lo que OpenStack denomina <a
href="http://docs.openstack.org/grizzly/openstack-network/admin/content/app_demo_routers_with_private_networks.html">
Per-tenant Routers with Private Networks</a>

En este caso utilizaremos <a
href='http://docs.openstack.org/havana/install-guide/install/apt/content/section_networking-routers-with-private-networks.html'>Per
tenant routers with private networks</a>, que permite que cada proyecto defina
sus propios routers y por tanto cada proyecto utilice su propio direccionamiento
IP de forma totalmente independiente al resto de proyectos.

<div style="text-align: center;"><img
src="http://docs.openstack.org/grizzly/openstack-network/admin/content/figures/1/figures/UseCase-MultiRouter.png"
alt="neutron setup"/></div>

## Redes privadas

Puesto que el despliegue completo incluye muchos elementos y puede resultar
demasiado complejo, vamos a tratar en esta sección el funcionamiento interno de
las redes privadas y en la siguiente sección veremos cómo se conectan al
exterior.

### Bridge de integración y túneles GRE

El objetivo a la hora de utilizar virtualización de redes es conseguir que las
instancias de una misma red se comuniquen entre sí utilizando el mismo
direccionamiento y compartiendo algunos elementos comunies (servidor DHCP,
puerta de enlace, etc.), pero que permenezcan completamente separadas del resto
de redes virtuales. Esto debe producirse independientemente del hipervisor (nodo
de computación) en el que se estén ejecutando las máquinas virtuales y una de
las formas de conseguir esto con OpenvSwitch es crear un switch virtual en cada
nodo, que se denomina switch de integración o br-int y comunicar cada nodo con
los demás a través de túneles GRE, que garantizan la seguridad de las
comunicaciones entre nodos.

Un esquema que representa esta situación tras una instalación limpia de
OpenStack neutron en cualquiera de los escenarios de prueba se ve en la
siguiente imagen:

<div style="text-align: center;"><img src="img/red-limpia.png" alt="red limpia"/></div>

### Verificación inicial

Puesto que vamos a ir mostrando paso a paso cómo se van creando algunos bridges
y puertos de conexión, vamos a mostrar cual es la situación de partida.

Inicialmente no hay ningún bridge linux:

	# brctl show
	bridge name	bridge id		STP enabled	interfaces

En el nodo de red hay tres puentes Open vSwitch (br-int, br-tun y br-ex). En
este apartado trabajaremos con el bridge de integración (br-int) y el bridge
para los túneles (br-tun):

	# ovs-vsctl list-br
	br-ex
	br-int
	br-tun

Esa misma salida en un nodo de computación, muestra que hay dos bridges Open
vSwitch:

	# ovs-vsctl list-br
	br-int
	br-tun

El único dispositivo conectado inicialmente a br-int es br-tun a través de una
interfaz especial denominada puerto patch:

	# ovs-ofctl show br-int
	OFPT_FEATURES_REPLY (xid=0x2): dpid:000032e2b607a841
	n_tables:254, n_buffers:256
	capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
	actions: OUTPUT SET_VLAN_VID SET_VLAN_PCP STRIP_VLAN SET_DL_SRC SET_D...
	 1(patch-tun): addr:fa:82:c1:68:15:38
	     config:     0
	     state:      0
	     speed: 0 Mbps now, 0 Mbps max
	 LOCAL(br-int): addr:32:e2:b6:07:a8:41
	     config:     0
	     state:      0
	     speed: 0 Mbps now, 0 Mbps max
	OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0

En el caso de br-tun existe un puerto para cada túnel GRE:

	# ovs-ofctl show br-tun
	OFPT_FEATURES_REPLY (xid=0x2): dpid:0000d2c93b314b42
	n_tables:254, n_buffers:256
	capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
	actions: OUTPUT SET_VLAN_VID SET_VLAN_PCP STRIP_VLAN SET_DL_SRC SET_DL_DST
	SET_NW_SRC SET_NW_DST SET_NW_TOS SET_TP_SRC SET_TP_DST ENQUEUE
	 1(patch-int): addr:56:63:30:9a:59:a7
	      config:     0
	      state:      0
	      speed: 0 Mbps now, 0 Mbps max
	 2(gre-1): addr:26:7c:0f:93:22:ee
	      config:     0
	      state:      0
	      speed: 0 Mbps now, 0 Mbps max
	 3(gre-2): addr:26:7c:0f:a3:12:ae
	      config:     0
	      state:      0
	      speed: 0 Mbps now, 0 Mbps max
	 LOCAL(br-tun): addr:d2:c9:3b:31:4b:42
	      config:     0
	      state:      0
	      speed: 0 Mbps now, 0 Mbps max
	OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0

### Creación de una red privada

Utilizando el cliente de línea de comandos de neutron y con las credenciales de
un usuario normal, creamos una red privada y una subred asociada a ella con el
rango de direcciones IP 10.0.0.0/24:

	$ neutron net-create privada
	Created a new network:
	+----------------+--------------------------------------+
	| Field          | Value                                |
	+----------------+--------------------------------------+
	| admin_state_up | True                                 |
	| id             | f84a328e-407e-4ca6-87bb-ec148853c585 |
	| name           | privada                              |
	| shared         | False                                |
	| status         | ACTIVE                               |
	| subnets        |                                      |
	| tenant_id      | 4beb810ce40f49659e0bca732e4f1a3c     |
	+----------------+--------------------------------------+

	$ neutron subnet-create f84a328e-407e-4ca6-87bb-ec148853c585 10.0.0.0/24
	Created a new subnet:
	+------------------+--------------------------------------------+
	| Field            | Value                                      |
	+------------------+--------------------------------------------+
	| allocation_pools | {"start": "10.0.0.2", "end": "10.0.0.254"} |
	| cidr             | 10.0.0.0/24                                |
	| dns_nameservers  |                                            |
	| enable_dhcp      | True                                       |
	| gateway_ip       | 10.0.0.1                                   |
	| host_routes      |                                            |
	| id               | d4bb2d0e-2af7-44fe-9729-4e3b95766e28       |
	| ip_version       | 4                                          |
	| name             |                                            |
	| network_id       | f84a328e-407e-4ca6-87bb-ec148853c585       |
	| tenant_id        | 4beb810ce40f49659e0bca732e4f1a3c           |
	+------------------+--------------------------------------------+

### Creación automática de la red virtual al levantar una instancia:

La red anteriormente definida no se crea realmente hasta que no se lance una
instancia, para la cual se crean todos los dispositivos de red necesarios y se
interconectan adecuadamente:

	$ nova boot --image 4ddb27ab-b3cc-4a65-ac52-f4ce7894e4ed \
	--flavor 1 \
	--key_name clave-openstack \
	--nic net-id=f84a328e-407e-4ca6-87bb-ec148853c585 \
	test

Comprobamos que aparecen un nuevo puerto en el br-int del nodo de red:

	# ovs-ofctl show br-int
	...
	 2(tap79712efb-32): addr:30:02:00:00:00:00
	     config:     PORT_DOWN
	     state:      LINK_DOWN
	     speed: 0 Mbps now, 0 Mbps max
	...

y un nuevo puerto en el nodo de computación:

	# ovs-ofctl show br-int
	...
	 2(qvob079c24c-83): addr:ae:49:25:f2:4a:1c
	     config:     0
	     state:      0
	     current:    10GB-FD COPPER
	     speed: 10000 Mbps now, 0 Mbps max
	...

Y vemos que se etiqueta automáticamente ambos nodos en una misma VLAN con el
número 1, tanto en el nodo de red:

	# ovs-vsctl show
	...
	        Port "tap79712efb-32"
	            tag: 1
	            Interface "tap79712efb-32"
	                type: internal
	...

Como en el nodo de computación:

	# ovs-vsctl show
	...
	    Bridge br-int
	        Port "qvob079c24c-83"
	            tag: 1
	            Interface "qvob079c24c-83"
	...

Para entender la numeración que utiliza neutron para los nuevos puertos y los
pasos que se siguen, vamos a ver en primer lugar los nuevos puertos que aparecen
en el proyecto (estos puertos son los que el usuario del proyecto puede ver):

	$ neutron port-list
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| id                                   | name | mac_address       | fixed_ips                                                                       |
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| 79712efb-320d-407d-82d7-eafd7eb51abe |      | fa:16:3e:dd:ff:a4 | {"subnet_id": "d4bb2d0e-2af7-44fe-9729-4e3b95766e28", "ip_address": "10.0.0.3"} |
	| b079c24c-8386-47db-a730-35b3a99419e8 |      | fa:16:3e:79:3b:ac | {"subnet_id": "d4bb2d0e-2af7-44fe-9729-4e3b95766e28", "ip_address": "10.0.0.2"} |
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+

El que tiene la dirección IP 10.0.0.3 se corresponde con un servidor DHCP que se ha 
creado (que veremos más adelante), el que tiene la dirección IP 10.0.0.2 se
corresponde con la máquina que hemos creado, por lo que nos quedamos con su uuid:

	 b079c24c-8386-47db-a730-35b3a99419e8

Como se tienen que crear varias interfaces de red asociadas a este puerto y el UUID es 
demasiado largo, neutron opta por utilizar solo los 11 primeros caracteres
(b079c24c-83).

Podemos comprobar que en el nodo de computación en el que se está ejecutando la
instancia aparecen 4 interfaces de red nuevas relacionadas con el identificador
b079c24c-83:

	# ip link show|grep qbrb079c24c-83
	13: qbrb079c24c-83: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
	14: qvob079c24c-83: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
	15: qvbb079c24c-83: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
	16: tapb079c24c-83: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 500

La forma en la que se interconectan todas estas interfaces se muestra en la siguiente
 imagen y es necesario añadir un bridge linux por cada instancia por la forma de definir
las reglas de seguridad por instancia:

<div style="text-align: center;"><img src="img/mv1.png" alt="mv1"/></div>

	qbr: Linux bridge
	qvo: Puerto en br-int
	qvb: Puerto en linux bridge
	tap: Interfaz de red de la máquina

Esto es algo que choca bastante al principio y hace más difícil entender los
elementos implicados, pero parece ser que existe una incompatibilidad
actualmente entre la utilización de Open vSwitch, iptables e interfaces tap, tal
como se explica en <a
href="http://docs.openstack.org/admin-guide-cloud/content/under_the_hood_openvswitch.html">OpenStack
Cloud Administration Guide</a>: *Ideally, the TAP device vnet0 would be
connected directly to the integration bridge, br-int. Unfortunately, this isn't
possible because of how OpenStack security groups are currently
implemented. OpenStack uses iptables rules on the TAP devices such as vnet0 to
implement security groups, and Open vSwitch is not compatible with iptables
rules that are applied directly on TAP devices that are connected to an Open
vSwitch port.*

Comprobamos el nuevo bridge linux que se ha creado con las interfaces tapb079c24c-83 
y qvbb079c24c-83

	# brctl show qbrb079c24c-83
	bridge name	bridge id		STP enabled	interfaces
	qbrb079c24c-83		8000.dea164299d40	no		qvbb079c24c-83
									tapb079c24c-83

### Servidor DHCP

Se utiliza linux network namespaces, un modo de virtualización de la red

En el nodo de red ejecutamos la instrucción:

	# ip netns
	qdhcp-f84a328e-407e-4ca6-87bb-ec148853c585

Vemos que se ha creado un espacio de nombres que empieza por dhcp, podemos ejecutar 
comandos en ese espacio de nombres de forma independiente del sistema:

	# ip netns exec qdhcp-f84a328e-407e-4ca6-87bb-ec148853c585 ip link show
	11: tap79712efb-32: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
	    link/ether fa:16:3e:dd:ff:a4 brd ff:ff:ff:ff:ff:ff
	12: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
	    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

Comprobamos que el dispositivo tap79712efb-32 está definido en este espacio de nombres.

Podemos ver también el proceso DHCP (con dnsmasq) que se está ejecutando en el
nodo de red  para darle IP dinámica a las instancias:

	# ps aux|grep f84a328e-407e-4ca6-87bb-ec148853c585
	nobody   19821  0.0  0.0  12888   652 ?        S    10:15   0:00 dnsmasq --no-hosts --no-resolv --strict-order --bind-interfaces --interface=tap79712efb-32 --except-interface=lo --pid-file=/var/lib/neutron/dhcp/f84a328e-407e-4ca6-87bb-ec148853c585/pid --dhcp-hostsfile=/var/lib/neutron/dhcp/f84a328e-407e-4ca6-87bb-ec148853c585/host --dhcp-optsfile=/var/lib/neutron/dhcp/f84a328e-407e-4ca6-87bb-ec148853c585/opts --leasefile-ro --dhcp-range=tag0,10.0.0.0,static,86400s --dhcp-lease-max=256 --conf-file= --domain=openstacklocal

Hay que destacar que la instrucción anterior incluye el parámetro "--no-resolv"
por lo que no se utilizará el fichero /etc/resolv.conf de la máquina donde se
ejecuta dnsmasq para definir los servidores DNS en los clientes DHCP. Esto
quiere decir que habrá que definir de forma explícita estos servidores DNS en
cada subred privada.

Podemos acceder al directorio:

	# cd /var/lib/neutron/dhcp/f84a328e-407e-4ca6-87bb-ec148853c585

Y comprobar que existe una reserva para la MAC de la instancia:

	# cat /var/lib/neutron/dhcp/f84a328e-407e-4ca6-87bb-ec148853c585/host 
	fa:16:3e:79:3b:ac,host-10-0-0-2.openstacklocal,10.0.0.2

### Conexión con otra instancia de la misma red

Para comprobar la conectividad entre instancias dentro de una misma red privada,
creamos una nueva instancia (test2) que se ejecutará en el segundo nodo de
computación y que virtualmente está en la misma red privada que la instancia
"test", aunque todo el tráfico pase por el bridge de integración de cada nodo de
computación, el bridge túnel y el túnel GRE.

Si listamos los puertos del proyecto al crear la nueva instancia, veremos que
aparece un puerto nuevo:

	$ neutron port-list
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| id                                   | name | mac_address       | fixed_ips                                                                       |
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| 6a6ac39c-61fb-46a5-bd39-e8ba1701d103 |      | fa:16:3e:eb:2c:d3 | {"subnet_id": "d4bb2d0e-2af7-44fe-9729-4e3b95766e28", "ip_address": "10.0.0.4"} |
	...

Luego tiene que haber nuevas interfaces de red en el segundo nodo de computación:

	# ip link show|grep 6a6ac39c-61
	17: qbr6a6ac39c-61: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
	18: qvo6a6ac39c-61: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
	19: qvb6a6ac39c-61: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
	20: tap6a6ac39c-61: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 500

Siguiendo el esquema de trabajo de la primera imagen, esta instancia se ha
creado en el segundo nodo de computación, por lo que comprobamos que
qvo6a6ac39c-61 está conectado a br-int:

	# ovs-ofctl show br-int
	...
	 2(qvo6a6ac39c-61): addr:0e:7e:7b:30:4a:bc
	     config:     0
	     state:      0
	     current:    10GB-FD COPPER
	     speed: 10000 Mbps now, 0 Mbps max
	...

Y vemos el nuevo bridge linux:

	# brctl show qbr6a6ac39c-61
	bridge name	bridge id		STP enabled	interfaces
	qbr6a6ac39c-61		8000.96a4ec71dea6	no		qvb6a6ac39c-61
									tap6a6ac39c-61

#### Reglas del grupo de seguridad

Comprobamos las reglas de iptables que se han creado en el nodo de computación
al levantar la instancia test (b079c24c-8386-47db-a730-35b3a99419e8), en este
caso se crean reglas utilizando sólo los 10 primeros caracteres del uuid: 

	# iptables -S |grep b079c24c-8 
	-N neutron-openvswi-ib079c24c-8
	-N neutron-openvswi-ob079c24c-8
	-N neutron-openvswi-sb079c24c-8
	-A neutron-openvswi-FORWARD -m physdev --physdev-out tapb079c24c-83 --physdev-is-bridged -j neutron-openvswi-sg-chain 
	-A neutron-openvswi-FORWARD -m physdev --physdev-in tapb079c24c-83 --physdev-is-bridged -j neutron-openvswi-sg-chain 
	-A neutron-openvswi-INPUT -m physdev --physdev-in tapb079c24c-83 --physdev-is-bridged -j neutron-openvswi-ob079c24c-8 
	-A neutron-openvswi-ib079c24c-8 -m state --state INVALID -j DROP 
	-A neutron-openvswi-ib079c24c-8 -m state --state RELATED,ESTABLISHED -j RETURN 
	-A neutron-openvswi-ib079c24c-8 -s 10.0.0.3/32 -p udp -m udp --sport 67 --dport 68 -j RETURN 
	-A neutron-openvswi-ib079c24c-8 -j neutron-openvswi-sg-fallback 
	-A neutron-openvswi-ob079c24c-8 -p udp -m udp --sport 68 --dport 67 -j RETURN 
	-A neutron-openvswi-ob079c24c-8 -j neutron-openvswi-sb079c24c-8 
	-A neutron-openvswi-ob079c24c-8 -p udp -m udp --sport 67 --dport 68 -j DROP 
	-A neutron-openvswi-ob079c24c-8 -m state --state INVALID -j DROP 
	-A neutron-openvswi-ob079c24c-8 -m state --state RELATED,ESTABLISHED -j RETURN 
	-A neutron-openvswi-ob079c24c-8 -j neutron-openvswi-sg-fallback 
	-A neutron-openvswi-sb079c24c-8 -s 10.0.0.2/32 -m mac --mac-source FA:16:3E:79:3B:AC -j RETURN 
	-A neutron-openvswi-sb079c24c-8 -j DROP 
	-A neutron-openvswi-sg-chain -m physdev --physdev-out tapb079c24c-83 --physdev-is-bridged -j neutron-openvswi-ib079c24c-8 
	-A neutron-openvswi-sg-chain -m physdev --physdev-in tapb079c24c-83 --physdev-is-bridged -j neutron-openvswi-ob079c24c-8 

Entre las que destacamos las siguientes:

* Se crean tres nuevas cadenas, para los procesos de entrada (i), salida (o) y prevenir spoofing (s):

	-N neutron-openvswi-ib079c24c-8
	-N neutron-openvswi-ob079c24c-8
	-N neutron-openvswi-sb079c24c-8

* Se permiten conexiones entrantes relacionadas o establecidas:

	-A neutron-openvswi-ib079c24c-8 -m state --state RELATED,ESTABLISHED -j RETURN 

* Se permiten las respuestas DHCP (DHCPOFFER y DHCPACK) desde el servidor DHCP de la red:

	-A neutron-openvswi-ib079c24c-8 -s 10.0.0.3/32 -p udp -m udp --sport 67 --dport 68 -j RETURN 

* Se permiten peticiones DHCP (DHCPDISCOVER y DHCPREQUEST):

	-A neutron-openvswi-ob079c24c-8 -p udp -m udp --sport 68 --dport 67 -j RETURN 

* Se permiten conexiones salientes relacionadas o establecidas:

	-A neutron-openvswi-ob079c24c-8 -m state --state RELATED,ESTABLISHED -j RETURN 

* Se verifica la correspondencia entre la dirección IP y la MAC:

	-A neutron-openvswi-sb079c24c-8 -s 10.0.0.2/32 -m mac --mac-source FA:16:3E:79:3B:AC -j RETURN 

#### Conectividad entre las instancias de la misma red

Como las reglas de cortafuegos se aplican a las instancias de forma individual y
no hay ninguna regla que permita acceso desde equipos del mismo segmento de red,
no es posible inicialmente acceder desde una instancia a otra de la misma red,
es necesario permitir explícitamente cada proceso que se desee. 

Dependiendo de la versión de OpenStack varían las reglas de seguridad que se
definen por defecto. En el caso de OpenStack Havana se definen las siguientes
reglas de entrada (ingress) y salida (egress):

	$ neutron security-group-rule-list
	+--------------------------------------+----------------+-----------+----------+------------------+--------------+
	| id                                   | security_group | direction | protocol | remote_ip_prefix | remote_group |
	+--------------------------------------+----------------+-----------+----------+------------------+--------------+
	| 68a447f4-95ef-4571-b209-17288bb17b2b | default        | ingress   |          |                  | default      |
	| 826d3068-9173-4477-be85-a304ab90a442 | default        | ingress   |          |                  | default      |
	| a008aa84-5543-4a24-8398-751b3aa3d1a8 | default        | egress    |          |                  |              |
	| ffa55db3-00ae-4e8f-8e03-92f76d998ba7 | default        | egress    |          |                  |              |
	+--------------------------------------+----------------+-----------+----------+------------------+--------------+
	
Añadimos una nueva regla para que puedan comunicarse por ICMP las máquinas de la red 10.0.0.0/24:

	$ neutron security-group-rule-create --direction ingress --protocol icmp --remote-ip-prefix 10.0.0.0/24 default
	
Que produce la siguiente regla de iptables:

    -A neutron-openvswi-ib079c24c-8 -s 10.0.0.0/24 -p icmp -j RETURN 

Y podemos comprobar que ya es posible hacer ping entre instancias de la misma
red privada.

Para permitir de forma explícita la comunicación a algún puerto UDP o TCP se
utilizaría una regla de seguridad como la siguiente para el servicio ssh
(22/tcp):

	$ neutron security-group-rule-create --direction ingress --protocol tcp --port-range-min 22 --port-range-max 22 --remote-ip-prefix 10.0.0.0/24 default

##### Reglas permisivas en la red privada

Si queremos que las instancias tengan acceso a todos los puertos del resto de
instancias de su misma red privada, podemos definir reglas de seguridad
permisivas para esta situación, abriendo todo el rango de puertos UDP y TCP: 

	$ neutron security-group-rule-create --direction ingress --protocol tcp --port-range-min 1 --port-range-max 65535 --remote-ip-prefix 10.0.0.0/24 default
	$ neutron security-group-rule-create --direction ingress --protocol udp --port-range-min 1 --port-range-max 65535 --remote-ip-prefix 10.0.0.0/24 default

### Redes independientes

Utilizamos otro proyecto y creamos una red y una subred con el mismo
direccionamiento 10.0.0.0/24 para verificar que neutron gestiona adecuadamente
esta situación sin que se produzcan interferencias.

Al lanzar la primera instancia, comprobamos los puertos que se han creado:

	$ neutron port-list
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| id                                   | name | mac_address       | fixed_ips                                                                       |
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| 03b89c06-87a6-4532-ac7c-397e0b537bfa |      | fa:16:3e:f8:83:5a | {"subnet_id": "16786d0d-2252-4b86-9f5b-e1e5741b0a14", "ip_address": "10.0.0.2"} |
	| 319ca3b4-5fe7-4f45-9b8a-eef0dfda0d87 |      | fa:16:3e:52:c2:89 | {"subnet_id": "16786d0d-2252-4b86-9f5b-e1e5741b0a14", "ip_address": "10.0.0.3"} |
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+

De nuevo aparecen dos nuevos puertos y lógicamente se ha creado toda la
estructura similar a la de antes, en particular un nuevo proceso DHCP en el nodo
de red:

	# ip netns
	qdhcp-f84a328e-407e-4ca6-87bb-ec148853c585
	qdhcp-60346ea0-ed9d-40aa-a615-5dd3e33892bc

	# ps aux |grep --color 60346ea0-ed9d-40aa-a615-5dd3e33892bc
	nobody    1142  0.0  0.0  12888   640 ?        S    13:26   0:00 dnsmasq --no-hosts --no-resolv --strict-order --bind-interfaces --interface=tap319ca3b4-5f --except-interface=lo --pid-file=/var/lib/neutron/dhcp/60346ea0-ed9d-40aa-a615-5dd3e33892bc/pid --dhcp-hostsfile=/var/lib/neutron/dhcp/60346ea0-ed9d-40aa-a615-5dd3e33892bc/host --dhcp-optsfile=/var/lib/neutron/dhcp/60346ea0-ed9d-40aa-a615-5dd3e33892bc/opts --leasefile-ro --dhcp-range=tag0,10.0.0.0,static,86400s --dhcp-lease-max=256 --conf-file= --domain=openstacklocal

Vemos que se ha etiquetado una nueva VLAN con el número 2:

	# ovs-vsctl show
	...
	        Port "qvo03b89c06-87"
	            tag: 2
	            Interface "qvo03b89c06-87"
	...

En la que están conectados el servidor DHCP y el nuevo bridge linux.

Un esquema que representa la creación de las tres instancias es el siguiente:

<div style="text-align: center;"><img src="img/red-2-tags.png" alt="red 2 tags"/></div>

## Conexión con el exterior

En la sección anterior explicamos los mecanismos que permiten la conectividad
entre instancias que se ejecutan en diferentes nodos de computación, así como el
aislamiento entre diferentes redes privadas mediante VLAN. En esta sección vamos
a explicar de forma detallada el proceso que se sigue para conectar las
instancias con el exterior.

## Red externa

Supongamos que partimos de una situación inicial en la que tenemos el bridge
exterior ya creado en el nodo de red y la interfaz eth1 conectada a él:

    # ovs-ofctl show br-ex
    OFPT_FEATURES_REPLY (xid=0x2): dpid:000000270e035840
    n_tables:254, n_buffers:256
    capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
    actions: OUTPUT SET_VLAN_VID SET_VLAN_PCP STRIP_VLAN SET_DL_SRC SET_DL_DST
    SET_NW_SRC SET_NW_DST SET_NW_TOS SET_TP_SRC SET_TP_DST ENQUEUE
     1(eth1): addr:00:27:0e:ff:a8:41
          config:     0
	  state:      0			
	  current:    1GB-FD AUTO_NEG
	  advertised: 10MB-HD 10MB-FD 100MB-HD 100MB-FD 1GB-HD 1GB-FD COPPER AUTO_NEG AUTO_PAUSE AUTO_PAUSE_ASYM
	  supported:  10MB-HD 10MB-FD 100MB-HD 100MB-FD 1GB-HD 1GB-FD COPPER AUTO_NEG
     	  speed: 100 Mbps now, 1000 Mbps max
     LOCAL(br-ex): addr:00:27:0e:03:58:40
          config:     0
     	  state:      0
     	  speed: 0 Mbps now, 0 Mbps max
    OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0

Esta situación se representa esquemáticamente en la siguiente figura, en la que
puede observarse que el bridge exterior está conectado a la interfaz de red
física eth1 y desconectado de los otros dos bridges:

<div style="text-align: center;"><img src="img/netnode.png" alt="netnode"/></div>

La dirección IP de br-ex es la 192.168.0.68 y está en una red 192.168.0.0/24 del
que podemos utilizar el rango 192.168.0.15-192.168.0.45 como direcciones IP
flotantes.

Creamos como administrador de neutron una red externa:

	# neutron net-create ext_net -- --router:external=True
	Created a new network:
	+---------------------------+--------------------------------------+
	| Field                     | Value                                |
	+---------------------------+--------------------------------------+
	| admin_state_up            | True                                 |
	| id                        | 3292768d-b916-45bc-8ce5-cbab121d6d01 |
	| name                      | ext_net                              |
	| provider:network_type     | gre                                  |
	| provider:physical_network |                                      |
	| provider:segmentation_id  | 3                                    |
	| router:external           | True                                 |
	| shared                    | False                                |
	| status                    | ACTIVE                               |
	| subnets                   |                                      |
	| tenant_id                 | 635dd2732ceb468e8518e556248c23d0     |
	+---------------------------+--------------------------------------+

Y asociamos una subred en la que especificamos el depósito de IP asignables:

	# neutron subnet-create --allocation-pool \
	start=192.168.0.15,end=192.168.0.45 ext_net 192.168.0.0/24 -- \
	--enable_dhcp=False
	Created a new subnet:
	+------------------+--------------------------------------------------+
	| Field            | Value                                            |
	+------------------+--------------------------------------------------+
	| allocation_pools | {"start": "192.168.0.15", "end": "192.168.0.45"} |
	| cidr             | 192.168.0.0/24                                   |
	| dns_nameservers  |                                                  |
	| enable_dhcp      | False                                            |
	| gateway_ip       | 192.168.0.1                                      |
	| host_routes      |                                                  |
	| id               | be85bfa6-6dd5-4b59-bdce-614e669c9f3e             |
	| ip_version       | 4                                                |
	| name             |                                                  |
	| network_id       | 3292768d-b916-45bc-8ce5-cbab121d6d01             |
	| tenant_id        | 635dd2732ceb468e8518e556248c23d0                 |
	+------------------+--------------------------------------------------+

Podemos entrar ahora en horizon con un usuario cualquiera y podemos observar en
"Topología de red" que ha aparecido una nueva red no gestionada por el usuario

Tenemos por un lado redes privadas gestionadas por cada proyecto y una red
exterior gestionada por el administrador, ¿qué elemento de red nos hace falta
ahora para poder conectar las redes privadas con la red exterior? 

## Routers

En el despliegue de OpenStack que estamos utilizando cada proyecto puede definir
sus propias redes privadas y routers, estos routers son los dispositivos que
permiten conectar las redes privadas con la red exterior y realmente lo que van
a hacer es conectar el bridge exterior con el bridge de integración.

Como usuario normal, definimos un nuevo router para conectar la red privada con
el exterior:

	$ neutron router-create r1
	Created a new router:
	+-----------------------+--------------------------------------+
	| Field                 | Value                                |
	+-----------------------+--------------------------------------+
	| admin_state_up        | True                                 |
	| external_gateway_info |                                      |
	| id                    | cc5fd2f5-59d6-484d-a759-819917a5610c |
	| name                  | r1                                   |
	| status                | ACTIVE                               |
	| tenant_id             | 4beb810ce40f49659e0bca732e4f1a3c     |
	+-----------------------+--------------------------------------+

### Conexión a la red exterior

Conectamos el router a la red exterior, lo que se denomina en neutron configurar
la puerta de enlace:

	$ neutron router-gateway-set cc5fd2f5-59d6-484d-a759-819917a5610c \
	3292768d-b916-45bc-8ce5-cbab121d6d01
	Set gateway for router cc5fd2f5-59d6-484d-a759-819917a5610c

Los routers también se crean en un espacio de nombres de red propio, por lo que
podemos comprobar con la instrucción *ip netns* que aparece un nuevo espacio de
nombres:

	# ip netns |grep cc5fd2f5-59d6-484d-a759-819917a5610c
	qrouter-cc5fd2f5-59d6-484d-a759-819917a5610c

Si vemos las direcciones IP de las interfaces de red en ese espacio de nombres,
comprobaremos que existe una interfaz de red con el prefijo "qj-" y de nuevo 11
caracteres asociados al nuevo puerto creado:

	# ip netns exec qrouter-cc5fd2f5-59d6-484d-a759-819917a5610c ip addr show
	27: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
	    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
	    inet6 ::1/128 scope host 
	       valid_lft forever preferred_lft forever
	28: qg-f14d9e65-26: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
	    link/ether fa:16:3e:b8:0a:f9 brd ff:ff:ff:ff:ff:ff
        inet 192.168.0.15/24 brd 192.168.0.255 scope global qg-f14d9e65-26
        inet6 fe80::f816:3eff:feb8:af9/64 scope link 
           valid_lft forever preferred_lft forever

Si comprobamos con las credenciales del administrador del cloud los puertos,
veremos que aparece un nuevo puerto asociado a la primera dirección IP de
nuestra red exterior (192.168.0.15):

	# neutron port-list|grep 192.168.0.15

	| f14d9e65-2608-4b1c-b17d-58c59ce84e8a |      | fa:16:3e:b8:0a:f9 | {"subnet_id": "be85bfa6-6dd5-4b59-bdce-614e669c9f3e", "ip_address": "192.168.0.15"} |

Desde cualquier equipo de la red exterior al que está conectado el nodo de red
podemos comprobar que tenemos conexión con el router recién creado:

	$ ping -c 3 192.168.0.15
	PING 192.168.0.15 (192.168.0.15) 56(84) bytes of data.
	64 bytes from 192.168.0.15: icmp_req=1 ttl=64 time=0.517 ms
	64 bytes from 192.168.0.15: icmp_req=2 ttl=64 time=0.163 ms
	64 bytes from 192.168.0.15: icmp_req=3 ttl=64 time=0.175 ms
	
	--- 192.168.0.15 ping statistics ---
	3 packets transmitted, 3 received, 0% packet loss, time 1998ms
	rtt min/avg/max/mdev = 0.163/0.285/0.517/0.164 ms

### Conexión a la red privada

Conectamos el router a la red privada (es necesario conocer el id de la subred)
mediante la instrucción:

    $ neutron router-interface-add cc5fd2f5-59d6-484d-a759-819917a5610c d4bb2d0e-2af7-44fe-9729-4e3b95766e28
	Added interface db992449-635f-4267-9d53-3ddb24a9acc1 to router cc5fd2f5-59d6-484d-a759-819917a5610c.

Y podremos comprobar que se ha creado un nuevo puerto en el proyecto asociado a
la dirección IP 10.0.0.1 (por defecto se reserva esta dirección IP en cada
subred para el router):

    $ neutron port-list
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	| id                                   | name | mac_address       | fixed_ips
	|
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
    ...
    | db992449-635f-4267-9d53-3ddb24a9acc1 |      | fa:16:3e:6b:cf:fa |
	{"subnet_id": "d4bb2d0e-2af7-44fe-9729-4e3b95766e28", "ip_address": "10.0.0.1"}
	|
	+--------------------------------------+------+-------------------+---------------------------------------------------------------------------------+
	
En el espacio de nombres del router aparece una nueva interfaz de red asociada a
este puerto con la dirección IP 10.0.0.1:

    # ip netns exec qrouter-cc5fd2f5-59d6-484d-a759-819917a5610c ip addr show
	...
    29: qr-db992449-63: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
    link/ether fa:16:3e:6b:cf:fa brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.1/24 brd 10.0.0.255 scope global qr-db992449-63
    inet6 fe80::f816:3eff:fe6b:cffa/64 scope link 
       valid_lft forever preferred_lft forever
					   
La denominación de la interfaz sigue el mismo criterio que en casos anteriores,
aunque en este caso se utiliza el prefijo "qr-" y posteriormente los 11 primeros
caracteres del puerto con el que se relaciona.

Esta interfaz de red está conectada al bridge de integración, como puede
verificarse mediante:

    # ovs-ofctl show br-int|grep qr-db992449-63
	8(qr-db992449-63): addr:7d:02:00:00:00:00

Puesto que tenemos una regla de seguridad que permite todo el protocolo ICMP
entre los equipos de la red 10.0.0.0/24, podemos hacer ping desde el router a la
instancia:

    # ip netns exec qrouter-cc5fd2f5-59d6-484d-a759-819917a5610c ping 10.0.0.2
	PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
	64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=0.378 ms
	...

OpenStack neutron añade automáticamente una regla de SNAT para que las
instancias de la red privada puedan acceder al exterior utilizando la dirección
IP exterior del router:

    # ip netns exec qrouter-cc5fd2f5-59d6-484d-a759-819917a5610c iptables -t nat -S|grep SNAT
    -A neutron-l3-agent-snat -s 10.0.0.0/24 -j SNAT --to-source 192.168.0.15 

Es decir, todas las máquinas de la red privada pueden acceder al exterior aunque
todavía no tengan asociada una dirección IP flotante. Las IPs flotantes se
utilizan para poder iniciar una conexión desde el exterior a una instancia
completa, por lo que se utilizan reglas de DNAT de iptables.

### IP flotante

Las IP flotantes se definen como las direcciones IPs asociadas a la red
exterior, que en un momento determinado se relacionan con una instancia y con su
dirección IP fija, pero que pueden relacionarse con otra instancia cuando se
desee. Las IPs flotantes permiten que las instancias sean accesibles desde el
exterior.

Solicitamos la asignación de una IP flotante al proyecto:

    $ neutron floatingip-create ext_net
	Created a new floatingip:
	+---------------------+--------------------------------------+
	| Field               | Value                                |
	+---------------------+--------------------------------------+
	| fixed_ip_address    |                                      |
	| floating_ip_address | 192.168.0.16                         |
	| floating_network_id | 3292768d-b916-45bc-8ce5-cbab121d6d01 |
	| id                  | bd31743b-79df-41a4-a40c-62756e868a88 |
	| port_id             |                                      |
	| router_id           |                                      |
	| tenant_id           | 4beb810ce40f49659e0bca732e4f1a3c     |
	+---------------------+--------------------------------------+

La IP flotante se asocia a un determinado puerto (en este caso al de la máquina
test) mediante:

    $ neutron floatingip-associate bd31743b-79df-41a4-a40c-62756e868a88 b079c24c-8386-47db-a730-35b3a99419e8
	Associated floatingip bd31743b-79df-41a4-a40c-62756e868a88

Si ahora vemos las reglas de la tabla nat de iptables en el espacio de nombres
del router observaremos las reglas de DNAT asociadas a la IP flotante 192.168.0.16:

    # ip netns exec qrouter-cc5fd2f5-59d6-484d-a759-819917a5610c iptables -t nat -S
	...
	-A neutron-l3-agent-OUTPUT -d 192.168.0.16/32 -j DNAT --to-destination 10.0.0.2 
	...
	-A neutron-l3-agent-PREROUTING -d 192.168.0.16/32 -j DNAT --to-destination 10.0.0.2 
	
Creamos reglas de seguridad para poder acceder a la instancia por ssh desde la
red 192.168.0.0/24:

    $ neutron security-group-rule-create --direction ingress --protocol tcp --port-range-min 22 --port-range-max 22 --remote-ip-prefix 192.168.0.0/24 default
	
Que lógicamente se relaciona con una nueva regla de cortafuegos que se crea en
la cadena ib079c24c-8 de la interfaz de red tapb079c24c-83 de la instancia
"test" en el nodo de computación:

    # iptables -S|grep ib079c24c-8
	...
	-A neutron-openvswi-ib079c24c-8 -s 192.168.0.0/24 -p tcp -m tcp --dport 22 -j RETURN 
	
Finalmente podemos ver la situación generada con todas sus interfaces de red y
direcciones IP en el siguiente diagrama:

<div style="text-align: center;"><img src="img/red-final.png" alt="red final"/></div>


### Referencias

* <a
  href="http://www.sdncentral.com/what-the-definition-of-software-defined-networking-sdn/">What’s
  Software-Defined Networking (SDN)?</a>
* <a
  href="http://www.networkworld.com/news/tech/2014/021114-sdn-network-functions-virtualization-278662.html">
  Understanding the differences between Software Defined Networking, network
  virtualization and Network Functions Virtualization</a>
* <a
  href="http://docs.openstack.org/grizzly/openstack-network/admin/content/ch_preface.html">OpenStack
  Networking Administration Guide</a>
* <a href="http://openstack.redhat.com/Networking_in_too_much_detail">RDO -
  Networking in too much detail </a>