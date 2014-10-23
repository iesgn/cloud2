---
layout: blog
tittle: Utilización de OpenStack (4). APIS
menu:
  - Unidad 5
---
##Creación de una instancia con la aplicación nova.

El objetivo de esta demostración es la creación de una instancia usando el clinete nova. Partimo de un escenario donde tenemos instalado el cliente (paquete *python-covaclient*) y vamos a ver los distintos pasos que tenemos que realizar:

1. Lo primero es bajarnos el fichero de credenciales y ejecutarlo en nuestro ordenador:

	![credenciales](img/demo1_1.png)

	Y la instalamoes en nuestro ordenador:
		
		$ source values.sh
		Please enter your Password:

2. A continuación vamos a crear un par de claves ssh que me permitan acceder a la instancia:
		
		$ nova keypair-add clave_acceso > ~/.ssh/clave-acceso.pem
		$ chmod 400 ~/.ssh/clave-acceso.pem

3. Ahora vamos acrear un grupo seguridad (cortafuegos) donde vamos a crear una regla que nos permita el acceso por ssh.

		$ nova secgroup-create gr_seguridad "Reglas de seguridad"
		+--------------------------------------+--------------+---------------------+
		| Id                                   | Name         | Description         |
		+--------------------------------------+--------------+---------------------+
		| 72479d3c-6dd3-45f6-9335-050793190209 | gr_seguridad | Reglas de seguridad |
		+--------------------------------------+--------------+---------------------+
		$ nova secgroup-add-rule gr_seguridad tcp 22 22 0.0.0.0/0
		+-------------+-----------+---------+-----------+--------------+
		| IP Protocol | From Port | To Port | IP Range  | Source Group |
		+-------------+-----------+---------+-----------+--------------+
		| tcp         | 22        | 22      | 0.0.0.0/0 |              |
		+-------------+-----------+---------+-----------+--------------+

4. Antés de crear la instancia vamos a ver las imágnes, flavor y redes que tenemos disponibles.

		$ nova image-list
		+--------------------------------------+--------------------------------------------------------+--------+--------+
		| ID                                   | Name                                                   | Status | Server |
		+--------------------------------------+--------------------------------------------------------+--------+--------+
		| 13e3a912-a0b6-4c6e-adeb-f05331a54e0a | CentOS 6.4 Minimal - 64 bits                           | ACTIVE |        |
		| 85da1e36-4720-424d-a88d-1e27a423b5ab | CentOS 6.5 Minimal - 64 bits                           | ACTIVE |        |
		| d7dead23-3b7f-4b81-8d6f-f5816a71b054 | CentOS 7.0 Minimal - 64 bits                           | ACTIVE |        |
		| ae329732-ba7c-47e5-b083-aa4c1099bf31 | CoreOS 367.1.0 - STABLE                                | ACTIVE |        |
		| 956294f7-1681-4583-b316-0a4f4cc13368 | Debian 7 (Wheezy) Stable - 64 bits                     | ACTIVE |        |
		| 1ca0ee93-3481-4da3-a2d1-8538683f0a07 | Debian 8 (Jessie) Testing - 64 bits                    | ACTIVE |        |
		| 22c6bd09-290d-4fc2-ae6e-41a190d42f73 | Fedora 19 Cloud - 64 bits                              | ACTIVE |        |
		| 9e5220ce-1d87-4b57-aea6-b2bf85aaa665 | Fedora 20 Cloud - 64 bits                              | ACTIVE |        |
		| 89c36ffc-94c0-4804-b7e6-27882ee5f045 | FreeBSD 10.0 Cloudimage - 64 bits                      | ACTIVE |        |
		| 1885f10b-a71f-48c1-a2b3-72453aaf5a65 | FreeBSD 9.2 Cloudimage - 64 bits                       | ACTIVE |        |
		| a7c757ec-214e-4370-a8ec-5869aac02eb3 | Ubuntu 10.04 LTS (Lucid Lynx) Server - 64 bits         | ACTIVE |        |
		| ab6e596f-3349-457b-8d9c-886f3718eabe | Ubuntu 11.04 (Natty Narwhal) Server 64 bits            | ACTIVE |        |
		| 4daa5361-5a16-44bc-8714-e990f169a826 | Ubuntu 12.04 LTS (Precise Pangolin) Server - 64 bits   | ACTIVE |        |
		| c4d67783-10a9-428d-9e5e-1beb5ab05222 | Ubuntu 13.04 (Raring Ringtail) Server - 64 bits        | ACTIVE |        |
		| e210bfa5-99c1-4aed-b7a1-8468bdbbe2b7 | Ubuntu 13.10 (Saucy Salamander) Server - 64 bits       | ACTIVE |        |
		| b1c15672-a7e5-466a-b4f1-89a49a364f84 | Ubuntu 14.04 LTS (Trusty Tahr) Beta 2 Server - 64 bits | ACTIVE |        |
		| 44288012-b805-455f-a21f-74ab36c46362 | Ubuntu 14.04 LTS - Trusty Tahr - 64 bits               | ACTIVE |        |
		| 2dde6e4b-413a-4cbe-8470-6e4c9f04ffe3 | Windows-2012R2-EVAL-64-bits                            | ACTIVE |        |
		| ef5a7de7-91be-4935-9126-8b8b2cac553c | debian6-recovery                                       | ACTIVE |        |
		+--------------------------------------+--------------------------------------------------------+--------+--------+

		$ nova flavor-list
		+----+--------------+-----------+------+-----------+------+-------+-------------+-----------+
		| ID | Name         | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
		+----+--------------+-----------+------+-----------+------+-------+-------------+-----------+
		| 20 | ssd.XXXS     | 1024      | 16   | 0         |      | 1     | 1.0         | True      |
		| 21 | ssd.XXS      | 2048      | 24   | 0         |      | 1     | 1.0         | True      |
		| 22 | ssd.XS       | 4096      | 48   | 0         |      | 2     | 1.0         | True      |
		| 23 | ssd.S        | 8192      | 96   | 0         |      | 4     | 1.0         | True      |
		| 24 | ssd.M        | 16384     | 192  | 0         |      | 8     | 1.0         | True      |
		| 25 | ssd.L        | 32768     | 384  | 0         |      | 16    | 1.0         | True      |
		| 26 | ssd.XL       | 49152     | 576  | 0         |      | 24    | 1.0         | True      |
		| 27 | ssd.XXL      | 65536     | 768  | 0         |      | 32    | 1.0         | True      |
		| 28 | ssd.XXXL     | 98304     | 1152 | 0         |      | 48    | 1.0         | True      |
		| 29 | ssd.XXXXL    | 131072    | 1536 | 0         |      | 64    | 1.0         | True      |
		| 30 | remote.XXXS  | 1024      | 0    | 0         |      | 1     | 1.0         | True      |
		| 31 | remote.XXS   | 2048      | 0    | 0         |      | 1     | 1.0         | True      |
		| 32 | remote.XS    | 4096      | 0    | 0         |      | 2     | 1.0         | True      |
		| 33 | remote.S     | 8192      | 0    | 0         |      | 4     | 1.0         | True      |
		| 34 | remote.M     | 16384     | 0    | 0         |      | 8     | 1.0         | True      |
		| 35 | remote.L     | 32768     | 0    | 0         |      | 16    | 1.0         | True      |
		| 36 | remote.XL    | 49152     | 0    | 0         |      | 24    | 1.0         | True      |
		| 37 | remote.XXL   | 65536     | 0    | 0         |      | 32    | 1.0         | True      |
		| 38 | remote.XXXL  | 98304     | 0    | 0         |      | 48    | 1.0         | True      |
		| 39 | remote.XXXXL | 131072    | 0    | 0         |      | 64    | 1.0         | True      |
		+----+--------------+-----------+------+-----------+------+-------+-------------+-----------+

		$ nova net-list
		+--------------------------------------+--------------+------+
		| ID                                   | Label        | CIDR |
		+--------------------------------------+--------------+------+
		| d15e0eb6-c892-4717-8be1-b3e85795e8c9 | ext-net      | -    |
		| d5d686b5-32fb-4e45-8809-98df3ee5ef3e | 00000061-net | -    |
		+--------------------------------------+--------------+------+

5. Ya podemos crear nuestra instancia. Vamos a crear una instancia *instancia_nova*) Ubuntu 14.04, con un sabor ssd.XXXS, el grupo de seguridad y las claves ssh que hemos creado anteriormente y conectado a la red 00000061-net.

		$ nova boot --flavor ssd.XXXS --image 44288012-b805-455f-a21f-74ab36c46362 --security-groups gr_seguridad --key-name clave_acceso --nic net-id=d5d686b5-32fb-4e45-8809-98df3ee5ef3e instancia_nova
		+--------------------------------------+---------------------------------------------------------------------------------+
		| Property                             | Value                                                                           |
		+--------------------------------------+---------------------------------------------------------------------------------+
		| OS-DCF:diskConfig                    | MANUAL                                                                          |
		| OS-EXT-AZ:availability_zone          | nova                                                                            |
		| OS-EXT-STS:power_state               | 0                                                                               |
		| OS-EXT-STS:task_state                | scheduling                                                                      |
		| OS-EXT-STS:vm_state                  | building                                                                        |
		| OS-SRV-USG:launched_at               | -                                                                               |
		| OS-SRV-USG:terminated_at             | -                                                                               |
		| accessIPv4                           |                                                                                 |
		| accessIPv6                           |                                                                                 |
		| adminPass                            | 2uG5JSjPBDrA                                                                    |
		| config_drive                         |                                                                                 |
		| created                              | 2014-10-23T07:26:22Z                                                            |
		| flavor                               | ssd.XXXS (20)                                                                   |
		| hostId                               |                                                                                 |
		| id                                   | 14f7c232-8e0b-4a74-8ca7-106bb11d39ad                                            |
		| image                                | Ubuntu 14.04 LTS - Trusty Tahr - 64 bits (44288012-b805-455f-a21f-74ab36c46362) |
		| key_name                             | clave_acceso                                                                    |
		| metadata                             | {}                                                                              |
		| name                                 | instancia_nova                                                                  |
		| os-extended-volumes:volumes_attached | []                                                                              |
		| progress                             | 0                                                                               |
		| security_groups                      | gr_seguridad                                                                    |
		| status                               | BUILD                                                                           |
		| tenant_id                            | 44f5cb63ad34481aab5cc9c2809e4a76                                                |
		| updated                              | 2014-10-23T07:26:22Z                                                            |
		| user_id                              | 7a15970a225d41babb750da8a6f5e8d2                                                |
		+--------------------------------------+---------------------------------------------------------------------------------+
6. Para acceder a la instancia, necesitamos asignarle una ip pública, para ello reservamos una nueva ip flotante y la asignamos a la instancia.

		$ nova floating-ip-pool-list
		+---------+
		| name    |
		+---------+
		| ext-net |
		+---------+

		$ nova floating-ip-create ext-net
		+--------------+-----------+----------+---------+
		| Ip           | Server Id | Fixed Ip | Pool    |
		+--------------+-----------+----------+---------+
		| 185.45.72.68 | -         | -        | ext-net |
		+--------------+-----------+----------+---------+

		$ nova floating-ip-associate instancia_nova 185.45.72.68

7. Y ya podemos acceder a la instancia:

		$ ssh -i ~/.ssh/clave-acceso.pem ubuntu@185.45.72.68
		Welcome to Ubuntu 14.04 LTS (GNU/Linux 3.13.0-24-generic x86_64)		

		 * Documentation:  https://help.ubuntu.com/		

		  System information as of Thu Oct 23 07:37:00 UTC 2014		

		  System load: 0.95              Memory usage: 5%   Processes:       69
		  Usage of /:  55.3% of 1.32GB   Swap usage:   0%   Users logged in: 0		

		  Graph this data and manage this system at:
		    https://landscape.canonical.com/		

		  Get cloud support with Ubuntu Advantage Cloud Guest:
		    http://www.ubuntu.com/business/services/cloud		

		0 packages can be updated.
		0 updates are security updates.		
		
		

		The programs included with the Ubuntu system are free software;
		the exact distribution terms for each program are described in the
		individual files in /usr/share/doc/*/copyright.		

		Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
		applicable law.		

		ubuntu@instancia-nova:~$ 

