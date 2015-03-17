---
layout: blog
tittle: Ejercicios
menu:
  - Unidades
---
## Ejercicio 1: Trabajar con instancias GNU/Linux

En este ejercicio, y siguiendo las guías que has estudiado durante esta sesión, 
tienes que crear una instancia a partir de una imagen de un sistema operativo Linux. Para ello recuerda los pasos que tienes que seguir:

* Crear el par de claves ssh
* Configura el cortafuegos de la red
* Reservar una IP flotante
* Crear una instancia, indicado el sabor, la red, el par de claves ssh y el grupo de seguridad que vas a utilizar.
* Asignar la dirección IP flotante a la instancia 
* Acceder a la instancia

Una vez que hemos accedido a la instancia podemos comprobar que tiene las características definidas al crearla:

* El nombre de la máquina es el nombre que le hemos dado a la instancia (cat */etc/hostname*).
* Según el sabor que hayamos elegido tendremos uno o dos discos duros del tamaño del sabor seleccionado, que podemos comprobar ejecutando: *sudo fdisk -l*
* También podemos comprobar la RAM que tiene la máquina ejecutando el comando *free*.

Si quieres hacer prácticas con intancias generadas con otras imágenes, puedes encontrar la manera de acceder a ellas en los siguientes artículos:

* [Centos 6.X and 7.0 default username and password](https://stackops.zendesk.com/hc/en-us/articles/201923327-Centos-6-4-and-6-5-default-username-and-password)
* [CoreOS default username and password](https://stackops.zendesk.com/hc/en-us/articles/201565975-CoreOS-default-username-and-password)
* [Debian default username and password](https://stackops.zendesk.com/hc/en-us/articles/201923397-Debian-default-username-and-password)
* [Fedora images default username and password](https://stackops.zendesk.com/hc/en-us/articles/201849976-Fedora-images-default-username-and-password)
* [Ubuntu default username and password](https://stackops.zendesk.com/hc/en-us/articles/202298618-Ubuntu-default-username-and-password)
* [FreeBSD default username and password](https://stackops.zendesk.com/hc/en-us/articles/201923427-FreeBSD-default-username-and-password)

## Ejercicio 2: Trabajar con instancias Windows

El objetivo de este ejercicio es realizar todo el proceso para crear una instancia con una distribución Windows.

Crea una instancia a partir de un sistema operativo Windows, teniendo en cuanta los siguientes aspectos:

* Tienes que abrir el puerto 3389 en el grupo de seguridad para poder acceder usando el protocolo RDP.
* Cuando trabajamos con instancias Windows no tiene sentido utilizar claves ssh para acceder a ellas.
* La primera vez que accedes a la instancia te pedirá que cambies la contraseña del Administrador, como se explica en el artículo: [Windows default username and password](https://stackops.zendesk.com/hc/en-us/articles/201565985-Windows-default-username-and-password).

Por medio de un cliente RDP, accede a la instancia que has creado utilizando la IP flotante que le has asignado.
