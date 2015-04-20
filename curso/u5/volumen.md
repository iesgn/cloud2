---
layout: blog
tittle: Creación de un volumen y asociación a una instancia con la aplicación nova
menu:
  - Unidades
---

### Asociación de un volumen a una instancia

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

### Creación de una instancia ejecutada sobre volumen

1. Visualizamos la lista de imágenes y de sabores que tenemos en nuestro sistema:

		$ nova image-list

		$ nova flavor-list

2. Creamos un volumen *bootable* desde una imagen:

		$ nova volume-create 8 --image-id 44288012-b805-455f-a21f-74ab36c46362 --display-name mi_disco
		+---------------------+--------------------------------------+
		| Property            | Value                                |
		+---------------------+--------------------------------------+
		| attachments         | []                                   |
		| availability_zone   | nova                                 |
		| bootable            | false                                |
		| created_at          | 2015-04-20T10:40:57.964980           |
		| display_description | -                                    |
		| display_name        | mi_disco                             |
		| id                  | 45f71394-2699-4c86-80da-cf8490f5a6c5 |
		| image_id            | 44288012-b805-455f-a21f-74ab36c46362 |
		| metadata            | {}                                   |
		| size                | 8                                    |
		| snapshot_id         | -                                    |
		| source_volid        | -                                    |
		| status              | creating                             |
		| volume_type         | None                                 |
		+---------------------+--------------------------------------+


		

3. Creamos una nueva instancia

		$ nova boot --flavor ssd.XXXS --boot-volume 45f71394-2699-4c86-80da-cf8490f5a6c5 --security-groups default --key-name mi_clave --nic net-id=d5d686b5-32fb-4e45-8809-98df3ee5ef3e instancia_nova
		+--------------------------------------+----------------------------------------------------------+
		| Property                             | Value                                                    |
		+--------------------------------------+----------------------------------------------------------+
		| OS-DCF:diskConfig                    | MANUAL                                                   |
		| OS-EXT-AZ:availability_zone          | nova                                                     |
		| OS-EXT-STS:power_state               | 0                                                        |
		| OS-EXT-STS:task_state                | spawning                                                 |
		| OS-EXT-STS:vm_state                  | building                                                 |
		| OS-SRV-USG:launched_at               | -                                                        |
		| OS-SRV-USG:terminated_at             | -                                                        |
		| accessIPv4                           |                                                          |
		| accessIPv6                           |                                                          |
		| adminPass                            | 2zdcUn4oCCKJ                                             |
		| config_drive                         |                                                          |
		| created                              | 2015-04-20T10:45:46Z                                     |
		| flavor                               | ssd.XXXS (20)                                            |
		| hostId                               | 962db8c7b201499f39eeea5cb2c88d73cae9931aa8703e1d3e2c8027 |
		| id                                   | c2b2c650-2f83-4c3e-b964-ebb916bf7e88                     |
		| image                                | Attempt to boot from volume - no image supplied          |
		| key_name                             | mi_clave                                                 |
		| metadata                             | {}                                                       |
		| name                                 | instancia_nova                                           |
		| os-extended-volumes:volumes_attached | [{"id": "45f71394-2699-4c86-80da-cf8490f5a6c5"}]         |
		| progress                             | 0                                                        |
		| security_groups                      | default                                                  |
		| status                               | BUILD                                                    |
		| tenant_id                            | 44f5cb63ad34481aab5cc9c2809e4a76                         |
		| updated                              | 2015-04-20T10:45:49Z                                     |
		| user_id                              | 7a15970a225d41babb750da8a6f5e8d2                         |
		+--------------------------------------+----------------------------------------------------------+


### Resumen de comandos

		# Listar volúmenes
		$ nova volume-list		

		# Crear un volumen
		$ nova volume-create --display-name NOMBRE SIZE		

		# Asociar un volumen a una instancia
		$ nova volume-attach SERVIDOR_ID VOLUMEN_ID DISPOSITIVO		

		# Desasociar un volumen de una instancia
		$ nova volume-detach SERVIDOR_ID VOLUMEN_ID		

		# Crear una instantánea de volumen
		$ nova volume-snapshot-create [opciones] VOLUMEN_ID

		# Crear un volumen a partir de una imagen
		$ nova volume-create SIZE --image-id IMAGEN_ID --display-name NOMBRE


