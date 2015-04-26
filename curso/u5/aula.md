---
layout: blog
tittle: Aplicación didáctica
menu:
  - Unidades
---

La gestión automática y ágil de la infraestructura ha llevado a lo que se
denomina en inglés *infrastructure as code*, es decir a la adopción por parte de
los administradores de sistemas de herramientas propias de las metodologías de
desarrollo ágil. De esta forma la infraestructura de una organización deja de
ser un elemento estático, configurado manualmente por el administrador de
sistemas de acuerdo a criterios propios y se convierte en un componente
integrado en el resto de su estructura IT.
La IaaS se ajusta muy bien a este nuevo paradigma y es muy habitual que para
gestionar la infraestructura en la nube se utilicen las siguientes herramientas:

* Gestión de la configuración: ansible, puppet, chef
* Sistemas de control de versiones: git
* Entornos de pruebas
* Integración continua
* APIs RESTful
* Colas de mensajes: AMQP

Este tema es ideal para que los alumnos, especialmente los de administración de
sistemas, empiecen a conocer estas herramientas y adquieran las destrezas
necesarias para gestionar su infraestructura como si se tratase de código y
exponemos algunos ejemplos de cómo llevar a cabo esto en clase:

* Aprender a utilizar Git, mejor si es con Github ya que permitirá dar
  visibilidad al trabajo de los alumnos.
* Aprender a utilizar un sistema de gestión de la configuración. De entre todos
  los que hay os recomendamos ansible por su simpleza y facilidad de
  aprendizaje. Ansible se podría utilizar para configurar una instancia del
  cloud una vez que se hubiese lanzado.
* Realizar pequeños scripts en bash para gestionar la infraestructura, dentro de
  un repositorio git y con libros de jugadas de ansible para su configuración
* Utilizar herramientas de orquestación de IaaS (heat en OpenStack) o de
  orquestación de entornos virtuales como vagrant
* Realizar pequeñas aplicaciones en un lenguaje de programación interpretado
  sencillo como python o ruby para interactuar con una nube de infraestructura
