---
layout: blog
tittle: Utilización de OpenStack (2)
menu:
  - Unidad 3
---
## Tarea: Trabajando con snapshots de volúmenes

**Objetivo:** Vamos a crear un servidor con un disco persistente, es decir, vamos a crear un disco arrancable con desde una imagen, lo vamos a configurar, y a continuación vamos a guardar un snapshot del disco. De esta manera podremos crear instancias con discos persistentes iguales de una manera muy sencilla.

1. Crea un disco arrancable a partir de una imagen de un sistema operativo linux.

2. Crea una instancia a partir de dicho disco, e instala el servidor web Apache2. Asigna a la instancia una IP pública y comprueba que podemos acceder al servidor web.

3. Crea una **Imagen del volumen**.

4. Crea otra instancia a partir del snapshot que acabos de crear, asigna a la nueva instancia otra IP pública y comprueba que tenemos acceso al servidor web.