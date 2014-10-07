---
layout: blog
tittle: Utilización de OpenStack (2)
menu:
  - Unidad 3
---
## Práctica: Gestión avanzada de instancias

1. Crea una instancia de tipo **ssd.XXXS**.

2. Comprueba que tienes 1 Gb de memoria RAM, y un disco de 16 Gb

		sudo free -l
		sudo fdisk -l

3. Redimensiona la instancia a un nu tipo **ssd.XXS**. (Recuerda que cuando aparezca el estado **VERIFY_RESIZE**, tienes que seleccionar la acción *Confirmar redimensionado*).

4. Comprueba que ahora tienes 2 Gb de memoria RAM, y un disco de 24 Gb.
