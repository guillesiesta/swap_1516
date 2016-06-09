#Práctica 6. Discos en RAID#

Como se ha indicado, partimos de una máquina virtual ya instalada y configurada a la que, estando apagada, añadiremos dos discos del mismo tipo y capacidad.

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica6/img/discos.png)

Ahora arrancamos la máquina y entramos para instalar el software necesario para configurar el RAID:

*guillesiesta@guillesiesta:~$* sudo apt-get install mdadm

Debemos buscar la información (identificación asignada por Linux) de ambos discos:

*guillesiesta@guillesiesta:~$* sudo fdisk -l

Ahora ya podemos crear el RAID 1, usando el dispositivo /dev/md0, indicando el número de dispositivos a utilizar (2), así como su ubicación:

*guillesiesta@guillesiesta:~$* sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc

En este punto, el dispositivo se habrá creado con el nombre /dev/md0, sin embargo, en cuanto reiniciemos la máquina, Linux lo renombrará y pasará a llamarlo /dev/md127.

Una vez creado el dispositivo RAID, y como aún no habremos reiniciado la máquina, usaremos /dev/md0 para darle formato:

*guillesiesta@guillesiesta:~$* sudo mkfs /dev/md0 

Por defecto, mkfs inicializa un dispositivo de almacenamiento con formato ext2. Ahora ya podemos crear el directorio en el que se montará la unidad del RAID:

*guillesiesta@guillesiesta:~$* sudo mkdir /dat

*guillesiesta@guillesiesta:~$* sudo mount /dev/md0 /dat

Podemos comprobar que el proceso se ha realizado adecuadamente, y también los parámetros con los que Linux ha conseguido montarlo usando la orden:

*guillesiesta@guillesiesta:~$* sudo mount

Para comprobar el estado del RAID, ejecutaremos:
 
*guillesiesta@guillesiesta:~$* sudo mdadm --detail /dev/md0

Para finalizar el proceso, conviene configurar el sistema para que monte el dispositivo RAID creado al arrancar el sistema. Para ello debemos editar el archivo /etc/fstab y añadir la línea correspondiente para montar automáticamente dicho dispositivo. Conviene utilizar el identificador único de cada dispositivo de almacenamiento en lugar de simplemente el nombre del dispositivo (aunque ambas opciones son válidas). Para obtener los UUID de todos los dispositivos de almacenamiento que tenemos, debemos
ejecutar la orden:

*guillesiesta@guillesiesta:~$* ls -l /dev/disk/by-uuid/

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica6/img/raids.png)

Anotaremos el correspondiente al dispositivo RAID que hemos creado.

Ahora ya podemos añadir al final del archivo /etc/fstab la línea para que monte automáticamente el dispositivo RAID.

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica6/img/fstab.png)

Finalmente, una vez que esté funcionando el dispositivo RAID, podemos simular un fallo en uno de los discos:

*guillesiesta@guillesiesta:~$* sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb


También podemos retirar “en caliente” el disco que está marcado como que ha fallado:

*guillesiesta@guillesiesta:~$* sudo mdadm --manage --remove /dev/md0 /dev/sdb

Y por último, podemos añadir, también “en caliente”, un nuevo disco que vendría a reemplazar al disco que hemos retirado:

*guillesiesta@guillesiesta:~$*sudo mdadm --manage --add /dev/md0 /dev/sdb

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica6/img/muestra1.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica6/img/muestra2.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica6/img/muestra3.png)