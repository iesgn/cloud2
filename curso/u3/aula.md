---
layout: blog
tittle: Aplicación didáctica
menu:
  - Unidades
---

El almacenamiento de volúmenes se utiliza en OpenStack para
almacenamiento "permanente", es decir para almacenar información de
forma independiente de la vida de la instancia. La información que se
almacena en el volumen puede ser el propio sistema operativo que se
ejecuta en la instancia o bien cualquier otra información como
aplicaciones adicionales o datos.

El almacenamiento de volúmenes complementa de forma muy interesante el tipo de
prácticas que se pueden realizar en el aula utilizando infraestructura en
nube. A continuación vamos a exponer algunos ejemplos que hemos utilizado estos
años y que consideramos que pueden resultar útiles.

## Gestión de datos independientes del sistema
Es muy habitual que los servidores reales utilicen un sistema adicional de
almacenamiento de tipo SAN para almacenar los datos de las aplicaciones, por lo
que utilizar volúmenes en una nube de IaaS se asemeja mucho a una situación real
y es además un sistema más sencillo de implementar y utilizar.

## Administración de servidores "reales"
En muchas ocasiones las prácticas son excesivamente académicas porque suelen
estar restringidas a un aspecto muy concreto para el que se monta un determinado
escenario que se elimina tras realizar la práctica. Al igual que en
programación no es lo mismo limitarse a escribir algoritmos que realizar
programas completos, en sistemas es muy apropiado que un alumno tenga que ir
configurando un servidor durante varios meses y le vaya añadiendo
funcionalidades de forma progresiva, a la vez que tiene que ir manteniendo el
sistema siempre funcional.
Esta práctica es muy adecuada realizarla en una instancia de la nube porque
ofrece un escenario al que pueden acceder tanto profesores como alumnos en
cualquier momento. Esto podría hacerse sobre una instancia normal, pero es mucho
más adecuado hacerlo sobre una instancia que tenga su sistema raiz sobre un
volumen, para que sea más estable.

## Copias de seguridad
Los volúmenes pueden utilizarse de forma muy realista y a la vez útil como
sistema de almacenamiento de copias de seguridad.

## Ejercicios con varios discos
Aunque desde un punto de vista de rendimiento y funcionalidad no sea demasiado
adecuado, con volúmenes podemos realizar todo tipo de prácticas que necesiten
utilizar varios discos adicionales, ya que crear volúmenes y asociarlos a una
instancia es un proceso trivial. Solemos utilizar volúmenes de OpenStack para
prácticas de particionado, formateo, volúmenes lógicos, discos dinámicos, RAID,
sistemas de ficheros distribuidos, sistemas de ficheros en cluster y redes de
almacenamiento tipo SAN.

