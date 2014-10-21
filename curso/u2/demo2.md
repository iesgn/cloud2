---
layout: blog
tittle: Utilización de OpenStack (1)
menu:
  - Unidad 2
---

## Demostración 2: Crear una instancia a partir de la imagen Debian (Asignar IP flotante, acceso a la instancia)

El objetivo de esta demostración es la creación de una instancia Linux a partir de una de las imágenes que disponemos en StackOps. Veamos los pasos que tenemos que dar para alcanzar nuestro objetivo:

1. Lo primero que vamos a hacer es crear nuestro par de claves, desde la
sección **Administrador de Seguridad** -> **LLaves** -> **Crear nuevo par de claves**:

	> **Par de claves ssh**: Utilizadas para acceder por ssh a las instancias desde fuera del cloud.

	Al crearse el par de claves, el sistema almacena la clave pública del usuario y envía al usuario la clave privada. Nos descargamos la clave privada, almacenándola en nuestro y le asignamos los permisos adecuados para que ningún otro usuario pueda leerla: *chmod 600*.