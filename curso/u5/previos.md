---
layout: blog
tittle: Conceptos previos
menu:
  - Unidades
---

Hasta ahora hemos utilizado un entorno web para gestionar nuestra
infraestructura ya que es más cómodo al principio para comprender
conceptos y es más adecuado en un curso como éste de iniciación a la
nube de infraestructura, pero en situaciones más reales es muy habitual utilizar
la línea de comandos o incluso programar con un lenguaje de alto nivel la
gestión de la infraestructura, principalmente cuando tenemos que realizar tareas
repetitivas o automáticas.
Una visión amplia de la administración de la infraestructura desde la
línea de comandos o la gestión de la infraestructura como código está
fuera de los objetivos de este curso, pero sí consideramos adecuado
una breve introducción a estos conceptos.

## API REST
Las API REST son un tipo de arquitecturas de desarrollo que utilizan
el protocolo HTTP y son ampliamente utilizadas hoy en día en Internet
para la extracción y modificación de datos automáticamente por
aplicaciones web y móviles. Las APIs REST no quedan ahí, sino que 
también se están convirtiendo en elementos cada vez más comunes en las
arquitecturas de sistemas ya que facilitan la programación de tareas y
la conexión sencilla entre diferentes aplicaciones.
Las principales características de REST son:
* Se define una o varias URIs para la comunicación entre el cliente y
el servidor, que proporciona una sintaxis universal para identificar
los recursos
* Se definen una serie de operaciones utilizando métodos HTTP GET,
POST, PUT y DELETE
* Se utiliza habitualmente un lenguaje de marcas para las peticiones y
las respuestas. Inicialmente el lenguaje más utilizado era XML, pero
cada vez se utiliza más JSON.

### Ejemplo de API REST
<a href="http://openweathermap.org/">OpenWeatherMap</a> es un sitio
web que ofrece una API REST pública sobre información meteorológica
muy sencilla de utilizar. Para obtener la información meteorológica actual de la
ciudad de Málaga basta hacer:

    wget -qO- "api.openweathermap.org/data/2.5/weather?q=Malaga,spain&units=metric"
    
En la petición incluimos la URL de la API y utilizamos un método GET
pasando como parámetros la ciudad y el formato de las unidades. Como
respuesta obtenemos un fichero en formato JSON que contiene la
información solicitada (se ha formateado la salida para que sea más
fácil ver la información):

    {"coord":{"lon":-4.42,
              "lat":36.72},
     "sys":{"message":0.4024,
	    "country":"ES",
	    "sunrise":1429594523,
	    "sunset":1429642638},
     "weather":[{"id":800,
                 "main":"Clear",
		 "description":"sky is clear",
		 "icon":"02d"}],
     "base":"stations",
     "main":{"temp":16.452,
             "temp_min":12.712,
	     "temp_max":18.321,
	     "pressure":1014.73,
	     "sea_level":1031.78,
	     "grnd_level":1014.73,
	     "humidity":94},
     "wind":{"speed":0.85,
             "deg":155.506},
     "clouds":{"all":8},
     "dt":1429602273,
     "id":2514256,
     "name":"Malaga",
     "cod":200}

## Componentes de OpenStack
OpenStack es un proyecto modular formado por diferentes componentes
independientes que ofrecen a los usuarios y al resto de componentes de
OpenStack una API REST con la cual interactuar. Los principales
componentes de OpenStack utilizados en este curso son:

|Nombre|Componente|Función|
|------|----------|-------|
|Nova|OpenStack Compute|Gestión de las instancias|
|Glance|OpenStack Image|Gestión de las imágenes|
|Keystone|OpenStack Identity|Gestión de usuarios, permisos y proyectos|
|Cinder|OpenStack Block Storage|Gestión de volúmenes|
|Neutron|OpenStack Networking|Gestión de las redes|

## Instalación de los clientes

Cada componente de OpenStack tiene su propio cliente de línea de
comandos con toda las funcionalidades, por lo que en principio
tendríamos que instalar los correspondientes a los cinco componentes
anteriores. Esto no va a ser necesario porque para el tipo de ejercicios que
vamos a realizar, en nuestro caso sólo vamos a utilizar los clientes de nova y
neutron.

### Instalación fácil
Si estás utilizando 

    # apt-get install python-novaclient python-neutronclient

Ya que

## Endpoints

Instalamos en el equipo el cliente de línea de comandos de OpenStack (con este método instalamos la última versión):

		# apt-get install python-pip
		# pip install python-novaclient
		# pip install python-neutronclient

## Descargar el fichero de credenciales

Una vez que tenemos instalados los clientes, tenemos que autentificarnos en el sistema, para ello, tenemos que bajar el fichero de credenciales y ejecutarlo en nuestro ordenador:

![credenciales](img/demo1_1.png)

Y la cargamos en nuestro ordenador:
		
		$ source values.sh
		Please enter your Password: