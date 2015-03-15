---
layout: blog
tittle: Creación de un volumen y asociación a una instancia con la aplicación nova
menu:
  - Unidades
---

En esta demostración vamos a crear un volumen y lo vamos a asociar a la instancia que hemos creado en la demostración anterior. Veamos los pasos:

1. Vamos a crear un volumen de 1 Gb.

		$ nova volume-create --display-name disco1 1
		+---------------------+--------------------------------------+
		| Property            | Value                                |
		+---------------------+--------------------------------------+
		| attachments         | []                                   |
		| availability_zone   | nova                                 |
		| bootable            | false                                |
		| created_at          | 2014-10-23T07:43:51.245111           |
		| display_description | -                                    |
		| display_name        | disco1                               |
		| id                  | 99138e3f-8a21-4da3-a50c-4ff4f6cecf5c |
		| metadata            | {}                                   |
		| size                | 1                                    |
		| snapshot_id         | -                                    |
		| source_volid        | -                                    |
		| status              | creating                             |
		| volume_type         | None                                 |
		+---------------------+--------------------------------------+

2. A continuación vamos a asociarlo a nuestra instancia:

		$ nova volume-attach instancia_nova 99138e3f-8a21-4da3-a50c-4ff4f6cecf5c /dev/vdb
		+----------+--------------------------------------+
		| Property | Value                                |
		+----------+--------------------------------------+
		| device   | /dev/vdb                             |
		| id       | 99138e3f-8a21-4da3-a50c-4ff4f6cecf5c |
		| serverId | 592d3bcf-a62e-4e0c-947f-64f09aa73ea4 |
		| volumeId | 99138e3f-8a21-4da3-a50c-4ff4f6cecf5c |
		+----------+--------------------------------------+

3. Accedemos a la instancia y comprobamos que el disco se ha conectado:

		ubuntu@instancia-nova:~$ sudo fdisk -l
		sudo: unable to resolve host instancia-nova		

		Disk /dev/vda: 17.2 GB, 17179869184 bytes
		4 heads, 32 sectors/track, 262144 cylinders, total 33554432 sectors
		Units = sectors of 1 * 512 = 512 bytes
		Sector size (logical/physical): 512 bytes / 512 bytes
		I/O size (minimum/optimal): 512 bytes / 512 bytes
		Disk identifier: 0x00013818		

		   Device Boot      Start         End      Blocks   Id  System
		/dev/vda1   *        2048    33554431    16776192   83  Linux		

		Disk /dev/vdb: 1073 MB, 1073741824 bytes
		16 heads, 63 sectors/track, 2080 cylinders, total 2097152 sectors
		Units = sectors of 1 * 512 = 512 bytes
		Sector size (logical/physical): 512 bytes / 512 bytes
		I/O size (minimum/optimal): 512 bytes / 512 bytes
		Disk identifier: 0x00000000		

		Disk /dev/vdb doesn't contain a valid partition table
		ubuntu@instancia-nova:~$ 
