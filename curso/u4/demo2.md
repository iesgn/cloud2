---
layout: blog
tittle: Utilización de OpenStack (3), redes
menu:
  - Unidad 4
---

## Demostración 2: Creación de una nueva instancia en la nueva red

Una vez creado nuestra nueva red, conectada a un nuevo router vamos a crear una instancia en la nueva red y vamos a comprobar que se le puede asociar una ip flotante, además vamos a comprobar que podemos hacer ping a otra instancia que está conectada a la otra red.

1. A la hora de crear la nueva instancia tenemos que asegurarnos que conectamos a la nueva red.

	![red](img/net5.png)

2. Podemos comprobar en la siguiente imagen que la nueva instancia tiene una IP fija en el rango de la nueva red (192.168.0.2) y que tiene asociada una ip pública.

	![red](img/net6.png)

3. Por último vamos a comprobar que puede hacer ping a otra instancia conectada a la red por defecto y que tiene como ip fija la 10.0.15.68.

	![red](img/net8.png)