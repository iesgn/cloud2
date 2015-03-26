---
layout: blog
tittle: Aplicación didáctica
menu:
  - Unidades
---

La utilización de IaaS en el ámbito académico proporciona dos ventajas
principalmente: agilidad a la hora de crear un entorno de trabajo y que la
secuencia de aprendizaje no venga condicionada por una configuración previa.

## Agilidad

El proceso mediante el cual un alumno dispone de un entorno de trabajo
completamente funcional es muy sencillo y apenas supone unos minutos. Este
entorno de trabajo es totalmente independiente del equipo en el que trabaja el
alumno, por lo que no es necesario apenas tener conocimientos previos para poder
utilizarlo y no depende ni del software ni del hardware que tenga dicho equipo.

En este tipo de entornos es muy sencillo eliminar las instancias que hubiera
configuradas previamente y volver a tener un escenario limpio a partir de
imágenes o instantáneas que el propio alumno vaya realizando.

## Secuencia de aprendizaje

Un problema que habitualmente nos encontramos en el aprendizaje práctico de
cualquier nueva tecnología es que es imprescindible contar con un escenario
configurado que nos permita empezar el proceso de aprendizaje. Habitualmente es
el alumno quien configura ese escenario en su propio equipo, bien directamente
sobre el sistema que tenga instalado, bien sobre una máquina virtual, pero esto
supone en muchas ocasiones un importante problema ya que el alumno carece en ese
momento de los conocimientos o habilidades precisas para realizar esta
configuración adecuadamente. 

Desde un punto de vista meramente pedagógico, sería mucho más conveniente poder
empezar a utilizar un software antes de tener que instalarlo y configurarlo. La
utilización de IaaS nos permite acercarnos más a este enfoque ya que permite la
creación previa de una imagen con toda la configuración necesaria por parte del
profesor y el alumno se limita a utilizarla dentro de su propio entorno sin
interferir con sus compañeros.

## Otras ventajas

* Se pueden crear fácilmente proyectos compartidos entre varios usuarios que
faciliten el trabajo en grupo
* Se puede configurar fácilmente el entorno para que otro usuario pueda acceder
a las instancias en cualquier momento. Esto es particularmente útil para el 
seguimiento o la corrección por parte del profesor.
* Si se utiliza una nube pública o una nube privada con VPN, las instancias
están disponibles para el alumno en todo momento, con independencia del equipo
que esté utilizando en cada momento.

## Ejemplos

Veamos algunos ejemplos en los que la agilidad y una secuencia de aprendizaje
más adecuada se ven beneficiados por la utilización de instancias de máquinas
virtuales en un entorno de IaaS.

#### Instalación de un servidor de bases de datos

Supongamos que durante los primeros días de clase queremos que cada alumno
disponga de un servidor de bases de datos correctamente configurado para su
utilización.

* El profesor crea una instancia en la que instala y configura el servidor de
bases de datos: Se crea una o varias bases de datos para realizar las prácticas,
se pueblan estas bases de datos si se estima conveniente, se crean los usuarios
en el SGBD, se asignan los permisos oportunos para que los usuarios puedan
acceder o no a determinadas funciones, etc.
* El profesor crea una instantánea de su instancia que se almacena como una
imagen y la hace pública para que todos los usuarios del cloud puedan
instanciarla.
* Cada alumno crea una instancia en su propio entorno de forma independiente y
puede comenzar a trabajar.
* Cada alumno puede crear instantáneas de su entorno de trabajo cuando estime
oportuno para en caso de problemas poder recuperar un escenario con el grado
de configuración que tuviera previamente.

Esto no implica que el alumno no tenga que realizar la instalación y
configuración de un servidor de bases de datos, sino que esta práctica se
realizará cuando se estime más oportuno y no vendrá condicionada por necesidades
previas.

#### Configuración de redes virtuales

La configuración de los gestores de máquinas virtuales y en particular las redes
virtuales en equipos de alumnos de primer curso suele provocar bastantes
problemas ya que no tienen los conocimientos previos para hacerlo
adecuadamente: Es complicado que 30 personas configuren adecuadamente las redes
en modo NAT o bridge cuando aún no tienen ni idea de lo que es una cosa u otra.

Obviamente esto no significa ni mucho menos que la utilización de máquinas
virtuales no deba ser importante en el aprendizaje de nuestros alumnos, sino de
nuevo, que este no debería venir condicionado por la necesidad de utilizarlo
para otro fin.

#### Despliegue de aplicaciones web

Son dos tareas y funciones totalmente independientes la configuración de un
servidor web o un servidor de aplicaciones y el despliegue de una aplicación
web. 

En entornos de IaaS es fácil configurar y distribuir entre los alumnos
servidores de aplicaciones con todas los paquetes, bibliotecas, gemas o
dependencias adecuadamente instaladas, tal como estarían en un entorno real y
que la tarea del alumno se centre exclusivamente en el despliegue de su
aplicación web, no en la configuración del servidor que hay debajo.

## Conclusiones

Esto no son más que algunos ejemplos suficientemente diversos para ver las
enormes posibilidades que se abren. En general, pueden plantearse prácticas más
complejas, inviables en el esquema tradicional de uso de máquinas virtuales por
la complejidad de configurar el escenario inicial y por los problemas que
acarrea una equivocación del alumno durante el desarrollo de la práctica. Además
las prácticas no interfieren con otras asignaturas, parar la práctica y
continuar otro día es tan simple como suspender la instancia y reanudarla cuando
se precise.
	
