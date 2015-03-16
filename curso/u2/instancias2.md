---
layout: blog
tittle: Operaciones sobre la instancia
menu:
  - Unidades
---

Una vez que hemos creado la instancia podemos realizar las siguientes operaciones sobre ella:

![acciones](img/acciones.png)

### Acciones del ciclo de vida de las intancias

![acciones](img/acciones2.png)

* **Pausar una instancia**: Paramos la ejecución de una instancia, guardando su estado en memoria RAM.
* **Suspender una instancia**: Paramos la ejecución de una instancia, guardando su estado en disco. Es similar al estado de himbernación.
* **Apagar una instancia**: Nos permite apagar la máuina virtual.
* **Encerder la instancia**: Si tenemos una instancia apagada, nos permite encenderla.
* **Reaunudar la instancia**: Si tenemos una intancia suspendida o pausada, no permite indicar que la máuina siga funcionando.
* **Reinciar la instancia**: Ejecuta un comando *reboot* en la instancia.
* **Terminar la instancia**: Destruye la intancia, liberando los recursos que tenía ocupado.

### Acciones de la configuración de red de la instancia

![acciones](img/acciones3.png)

Este apartado lo veremos más detenidamente cuando estudiemos la gestión de las redes en OpenStack, pero como podemos observar podemos añadir o eliminar nuevas interfaces de red a nuestra intancia, y cómo hemos visto anteriomente podemos gestionar la asosiación de las ip flotantes.

### Acciones sobre la configuración de la seguridad de la instancia

![acciones](img/acciones4.png)

* Aunque se suele utilizar las claves ssh para el acceso de las intancias, en el primer apartado podemos **cambiar la contraseña para el acceso a la instancia.**
* Podemos acceder a **la información de la clave pública que se ha usado al crear la instancia.**
* Podemos **administrar los Grupos de Seguridad de nuestra instancia**, pudiendo abrir o cerrar un puerto determinado.

### Acciones de la configuración del almacenamiento de la instancia

![acciones](img/acciones5.png)

Como estudiaremos en la unidad de [Volúmenes](http://iesgn.github.io/cloud2/curso/u3/), podemos **asociar o desasociar un volumen a una intancia**. Además cómo veremos en la segunda sesión de la unidad de *Instancias* podemos realizar una **instantánea o una copia de seguridad** de nuestra instancia.

### Acciones de gestión de las instancias

![acciones](img/acciones6.png)

En la próxima sesión de esta unidad también estudiaremos la **redimensión y es recate de las intancias.
