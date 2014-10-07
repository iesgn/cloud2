---
layout: blog
tittle: Utilización de OpenStack (2)
menu:
  - Unidad 3
---
## Práctica: Trabajar con Volúmenes

### Ejercicio 1

1. Crea un volumen (disco) de 16 Gb y asocialo a una instancia que tengas funcionando.

2. Comprueba que el disco se ha conectado a la máquina, formatea el disco, móntalo y crea un fichero de texto.

		# fdisk -l
		# mkfs.ext4 /dev/vdb
		# mount /dev/vdb /mnt
		# cd /mnt
		# touch fichero.txt
		# umount /mnt

3. Desasocia el disco de la instancia.

4. Vuelve asociarlo a otra instancia que tengas ejecutándose y comprueba que la información del disco no se ha perdido.

### Ejercicio 2

1. Crea un disco a partir de una imagen de un sistema operativo. Este disco será arrancable.

2. Crea una instancia cuyo origen sea el disco que acabamos de hacer.

3. Accede a la máquina, instala el servidor web Apache2 y comprueba que podemos acceder al mismo desde un navegador.

4. Destruye la máquina. Se ha borrado la instancia sin embargo el contenido del disco no se ha eliminado.

5. Crea una nueva instancia a partir del disco y comprueba que tiene el servidor web instalado.
