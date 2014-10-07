---
layout: blog
tittle: Utilización de OpenStack (1)
menu:
  - Unidad 2
---
## Práctica: Trabajar con Instancias GNU/Linux

El objetivo de esta práctica es aprender a lanzar instancias GNU/Linux y acceder a ellas mediante ssh.

Las imágenes predefinidas desde las que se crean las instanicas son compartidas por todos los usuarios, por lo que habitualmente no tienen definida una contraseña de acceso, ya que cualquier otro usuario del cloud podría acceder no solo a sus instancias sino a la del resto de usuarios. Es por ello por lo que el software de IaaS, inyecta en la instancia recién creada la clave ssh pública del usuario y solo él podrá acceder a la instancia con su clave privada, garantizándose así que no puedan acceder otros usuarios del cloud.

Los pasos que tenemos que realizar son los siguientes:

1. Accede a **StackOps Portal** con la URL
[https://cloud.stackops.net](https://cloud.stackops.net) e introduce el nombre de usuario y la contraseña que te han asignado.

2. Familiarízate con el entorno navegando por los distintos iconos del portal:

	> **Imagen**: Imagen de sistema preconfigurado que se utiliza como base para crear instancias. Dentro del cloud podemos encontrar diferentes imágenes para cada tipo de instacia que se quiera utilizar.

3. Comprueba las imágenes que tenemos a nuestra disposición en el apartado
**Catálogo de Imágenes**.

	> **Par de claves ssh**: Utilizadas para acceder por ssh a las instancias desde fuera del cloud.

4. Antes de crear la instancia vamos a crear nuestro par de claves, desde la sección **Administrador de Seguridad** -> **Llaves** -> **Crear nuevo par de claves**: crea un par de claves.

	Al crearse el par de claves, el sistema almacena la clave pública del usuario y envía al usuario la clave privada. Descárgate la clave privada, almacénala en tu equipo y asígnale los permisos adecuados para que ningún otro usuario pueda leerla: chmod 600.

	> **Instancia (Servidor Virtual)**: Clon de una imagen que se crea a demanda del usuario en uno de los nodos del cloud.

	> **Un sabor o tipo de la instancia (flavor)**: Define para una instancia el número de cores virtuales, la RAM, si dispone o no de discos efímeros, etc.

5. Crea una instancia de una imagen GNU/Linux (**Administrador de Servidores** -> **Crear Instancias**).

	Los datos necesarios para crear la instancia son los siguientes:

	* **Nombre** de la instancia
	* **Tipo de fuente**: Puede ser Imagen, Instantanea o Volumen, elegimos la
       primera opción.
	* **Tipo de la instancia**: El **sabor** de la máquina que vamos a crear.
	* **Fuente**: Podemos elegir de la lista de imágenes disponibles. Vamos a
       hacer una instancia a partir de la imagen **Debian 7 (Wheezy) Stable - 64 bits**.
	* Número de instancias, por defecto 1.
	* La zona por defecto es **nova**.
	* En la pestaña **Redes**, la **red** a la que está conectada nuestra instancia.
	* En la pestaña de **Seguridad**, ll par de **claves SSH** con el que vamos a acceder a la instancia y el **Grupo de Seguridad** habilitado.
	

	Accede a ella utilizando la consola, para ello pulsa sobre el nombre de la instancia (desde el apartado **Administrador de Servidores**) y a la pestaña **Consola Remota**.

	> **IP fija**: Dirección IP con la que se crean las instancias y que se utiliza para comunicación interna.

	> **IP flotante**: En nuestro caso es una IP pública, y es la dirección IP que puede asociarse a diferentes instancias con el fin de acceder a ellas desde fuera.

6. Cuando se crea, la instancia toma por DHCP una dirección IP fija de la red a la que está conectada. Durante la vida de la instancia está IP será siempre la misma, de ahí el nombre de IP fija, a pesar de que se asigna por DHCP.

7.  Para poder acceder a nuestra instancia desde el exterior tenemos que asignarle a la instancia una IP flotante, para ello desde el apartado **Administrador de Seguridad**->**IP públicas** solicita una nueva IP flotante (**Asignar nueva IP**) y asígnala a la instancia que has creado. Podemos desasociar esta IP flotante de una instancia y asignarla a otra, de ahí el nombre de IP flotante, que puede cambiar de una instancia a otra.

	> **Grupo de seguridad**: Reglas de cortafuegos (iptables) que controlan el acceso a las instancias.

8. También es necesario abrir el puerto 22 en el Grupo de seguridad default (**Administrador de Seguridad**->**Cortafuegos**). Edita las reglas y añade una nueva en la que permitas el acceso por el puero 22 desde la IP de tu ordenador.

9. Por último, utilizando un cliente ssh accede a la instancia utilizando la ip flotante que le hemos asignado a la instancia. Por ejemplo:

        ssh -i clave_ssh.pem debian@185.45.72.142

10. Una vez que has trabajado un poco con la intancia, practica las distintas operaciones que puedes hacer sobre ella: pausar, suspender, reiniciar, terminar, ...

11. Una vez que hemos accedido a la instancia podemos comprobar que tiene las características definidas al crearla:

	* El nombre de la máquina es el nombre que le hemos dado a la instancia (cat */etc/hostname*).
	* Según el sabor que hayamos elegido tendremos uno o dos discos duros del tamaño del sabor seleccionado, que podemos comprobar ejecutando: *sudo fdisk -l*
	* También podemos comprobar la RAM que tiene la máquina ejecutando el comando *free*.

12. Si quieres hacer prácticas con intancias generadas con otras imágenes, puedes encontrar la manera de acceder a ellas en los siguientes artículos:

* [Centos 6.4 and 6.5 default username and password](https://stackops.zendesk.com/hc/en-us/articles/201923327-Centos-6-4-and-6-5-default-username-and-password)
* [CoreOS default username and password](https://stackops.zendesk.com/hc/en-us/articles/201565975-CoreOS-default-username-and-password)
* [Debian default username and password](https://stackops.zendesk.com/hc/en-us/articles/201923397-Debian-default-username-and-password)
* [Fedora images default username and password](https://stackops.zendesk.com/hc/en-us/articles/201849976-Fedora-images-default-username-and-password)
* [Ubuntu default username and password](https://stackops.zendesk.com/hc/en-us/articles/202298618-Ubuntu-default-username-and-password)
* [FreeBSD default username and password](https://stackops.zendesk.com/hc/en-us/articles/201923427-FreeBSD-default-username-and-password)
