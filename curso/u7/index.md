---
layout: blog
tittle: Cloud Computing en Educación	
menu:
  - Unidades
---

### Objetivos

* Comentar las ventajas e inconvenientes del uso de IaaS y PaaS en los
  diferentes ciclos de formación profesional.
* Analizar las posibilidades de utilización de una nube pública gratuita o en
  pago por uso en un centro educativo.
* Analizar las posibilidades reales de implantación de una nube privada en un
  centro educativo.
* Presentar distintas posibilidades para la instalación de un entorno
  de prueba de OpenStack.

### Contenidos

* [Cloud computing en el aula](presentacion_cloud_aula.html)
* [Alternativas al cloud privado de IaaS](presentacion_iaas_publico)
* [Implantación de una nube privada en un centro educativo](presentacion_implantacion.html)
* [Introducción a la implantación de OpenStack](presentacion_instalacion_openstack.html)

### Tarea final del curso

realiza un documento o un vídeo donde describes los pasos para realizar los siguientes ejercicios:

1. Desarrolla el siguiente escenario usando la aplicación web Cirrusflex:

	* Crea una red interna con direccionamiento 192.168.0.0/24.
	* Crea un router que conecta la red interna con la red pública.
	* Crea un par de claves ssh.
	* Crea una instancia a la que podamos acceder usando la clave ssh anteriormente creada y conectada a la red que has creado. ¿Qué grupo de seguridad has usado? ¿Qué reglas tiene definida?
	* Instala un servidor web en la instancia y comprueba que funciona accediendo con un cliente web. 
	* Realiza un snapshot a la instancia.
	* Conecta un volumen a la instancia.
	* Accede por ssh a la instancia y formatea el volumen.

2. Elimina todo el escenario que has creado en el ejercicio anterior a excepción del snapshot.

3. Desarrolla el siguiente escenario utilizando los clientes *nova* y *neutron*:

	* Crea una red interna con direccionamiento 192.168.10.0/24.
	* Crea un router que conecta la red interna con la red pública.
	* Crea un par de claves ssh.
	* Crea una instancia, apartir del snapshot que hiciste en el ejercicio 1, a la que podamos acceder usando la clave ssh anteriormente creada y conectada a la red que has creado.
	* Comprueba que tiene corriendo un servidor web accediendo con un cliente web. 
	* Conecta un volumen a la instancia.
	* Utiliza el cliente nova para mostrar la lista de instancias, la lista de redes y la lista de volúmenes.

