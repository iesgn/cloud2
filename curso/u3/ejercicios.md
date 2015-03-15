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

## Ejercicio 2: Trabajando con snapshots de volúmenes

**Objetivo:** Vamos a crear un servidor con un disco persistente, es decir, vamos a crear un disco arrancable desde una imagen, lo vamos a configurar, y a continuación vamos a guardar un snapshot del disco. De esta manera podremos crear instancias con discos persistentes iguales de una manera muy sencilla.

1. Crea un disco arrancable a partir de una imagen de un sistema operativo linux.

2. Crea una instancia a partir de dicho disco, e instala el servidor web Apache2. Asigna a la instancia una IP pública y comprueba que podemos acceder al servidor web.

3. Crea una **Imagen del volumen**.

4. Crea otra instancia a partir del snapshot que acabos de crear, asigna a la nueva instancia otra IP pública y comprueba que tenemos acceso al servidor web.

