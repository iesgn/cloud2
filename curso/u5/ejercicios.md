---
layout: blog
tittle: Ejercicios
menu:
  - Unidades
---
## Ejercicio 1

Instala el cliente *nova*, usa el fichero de credenciales para autentificarte en tu proyecto y realiza los siguientes ejercicios:

1. Visualiza las imágenes, instancias, redes, sabores, claves ssh y grupos de seguridad de tu proyecto.
2. Crea una instancia Ubuntu, reserva y asocia una IP flotante a la misma y accede a ella.
3. Realiza alguna operación sobre la instancia: pausa, suspende, reinicia,...
4. Realiza alguna modificación en la instancia y crea un snapshot de la misma.
5. Termina la instancia.
6. Libera la IP flotante.
7. Crea una nueva clave ssh y un nuevo grupo de seguridad con las reglas que necesites.
8. Crea una nueva instancia a partir del snapshot, con la nueva clave ssh y utilizando el nuevo grupo de seguridad, reserva y asocia una nueva IP flotante.
9. Crea un volumen de 5Gb.
10. Asocia el volumen a la instancia.
11. Accede a la instancia y comprueba que está creada a partir del snapshot y que hemos conectado un volumen.

## Ejercicio 2

Instala el cliente *neutron* y crea el escenario de red que vimos en la tema anterior.

![red](http://iesgn.github.io/cloud2/curso/u4/img/red3.png)

1. Crea una nueva red y una subred asociada.
2. Crea un router conectado a la red exterior.
3. Crea la interfaz del router que lo conecta a la red.
4. Con el cliente *nova* crea las dos instancias en cada una de las redes y comprueba si tienen conectividad.


