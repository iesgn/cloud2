---
layout: blog
tittle: Utilización de OpenStack (1)
menu:
  - Unidad 2
---
## Tarea: Trabajando con snapshots

1. Crea una instancia de un sistema operativo Linux.

2. Una vez que has accedido al sistema instala el servidor web apache2:

		aptitude install apache2

3. Desde la máquina cliente comprueba que está funcionando el servidor web, accediendo con un navegador web a la IP flotante que le has asignado a la instancia. (Recuerda permitir el acceso por el puerto 80 en el grupo de seguridad "default").

4. Crea un snapshot, llámalo "LinuxApache_TuNombre", para ello en el menú desplegable de acciones de la instancia de la que quiere realizarse una instantánea se selecciona la acción **Almacenamiento**->**Backups e Instantáneas**.

5. Una vez finalizada la creación del snapshot, crea una instancia a partir de ella, asígnale una IP flotante y comprueba desde un navegador que ya tiene el servidor web instalado.

