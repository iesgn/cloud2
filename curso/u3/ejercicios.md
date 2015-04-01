---
layout: blog
tittle: Ejercicios
menu:
  - Unidades
---
## Ejercicio 1: Trabajar con volúmenes

1. Crea un volumen (disco) de 16 Gb y asocialo a una instancia que tengas funcionando.

2. Comprueba que el disco se ha conectado a la máquina, formatea el disco, móntalo y crea un fichero de texto.

		# sudo fdisk -l
		# sudo mkfs.ext4 /dev/vdb
		# sudo mount /dev/vdb /mnt
		# cd /mnt
		# sudo touch fichero.txt
		# sudo umount /mnt

3. Desasocia el disco de la instancia.

4. Vuelve asociarlo a otra instancia que tengas ejecutándose y comprueba que la información del disco no se ha perdido.

## Ejercicio 2: Instancias ejecutadas sobre volúmenes

**Objetivo:** Vamos a crear un servidor con un disco persistente, es decir, vamos a crear un disco arrancable desde una imagen, y vamos a crear una instancia cuyo disco raíz sea el volumen que hemos creado. Vamos a comprobar que los cambios que realizamos sobre ese disco no se van a perder.lo vamos a configurar, 

1. Crea un volumen a partir de una imagen de un sistema operativo linux.

2. Crea una instancia a partir de dicho disco, e instala el servidor web Apache2. Asigna a la instancia una IP pública y comprueba que podemos acceder al servidor web.

3. Termina la instancia.

4. Crea otra instancia a partir del volumen que acabos de crear, asigna a la nueva instancia otra IP pública y comprueba que tenemos acceso al servidor web.

5. Comprueba que el tamaño del disco de la instancia es el tamaño del volumen que hemos creado independientemente del sabor que hayamos elegido a la hora de crear la instancia.


