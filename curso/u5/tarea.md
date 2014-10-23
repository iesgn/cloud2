---
layout: blog
tittle: Utilización de OpenStack (4). APIS
menu:
  - Unidad 5
---
###Tarea

1. Utilizando el cliente neutron crea la siguiente infraestructura de red.

	![red](../u4/img/red3.png)

2. Crea una instancia con el cliente nova, conectada a la red *"mi_red"*. tienes que crear un nuevo grupo de seguridad y una nueva clave ssh. Asocia a la instancia una ip pública.

3. Crea con el cliente nova un nuevo disco y asocialó a la instancia, accede a la instancia y comprueba que el disco está conectado.