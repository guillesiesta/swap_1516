#Practica 2.- Clonando un sitio web

Antes de nada, para mayor comodidad, voy a añadir en el archivo /etc/hosts la IP de la máquina contraria para así que baste con solo poner el nombre de la máquina.

<u>Ubuntu server 1</u>

*guillesiesta@guillesiesta:~$* sudo nano /etc/hosts

172.16.68.131 userver2

<u>Ubuntu server 2</u>

*guillesiesta@guillesiesta:~$* sudo nano /etc/hosts

172.16.68.130 userver1

##Prueba de copia por ssh##

Creo en userver1 un archivo ssh_clona.txt y lo envío a userver2 comprimido en tar.

*guillesiesta@guillesiesta:~$* sudo nano ssh_clona.txt

*guillesiesta@guillesiesta:~$* tar czf - ssh_clona.txt | ssh userver2 'cat > ~/tar.gz'

En userver2 nos aparecerá en el directorio root el archivo tar.gz. Lo descomprimimos y comprobamos que dentro está el archivo ssh_clona.txt. 

##Instalar la herramienta rsync##

Primero, la instalo en las dos máquinas:

*guillesiesta@guillesiesta:~$* sudo apt-get install rsync

Una vez instalado, creamos varios archivos .txt en la carpeta /var/www de userver1, así la modificamos para posteriormente comprobar si se copian correctamente los archivos en userver2.

*guillesiesta@guillesiesta:~$* sudo nano /var/www/copiamos.txt

Una vez creado el archivo, introducimos el comando en nuestra userver2 que nos hará copiar toda la
carpeta /var/www de userver1.

*guillesiesta@guillesiesta:~$* sudo rsync -avz -e ssh root@userver1:/var/www/  /var/www/

Tras introducir la contraseña de userver1 se comprueba haciendo un ls -l /var/www para comprobar que se ha copiado toda la carpeta de userver1 en userver2.

##Acceso sin contraseña para ssh##

Primero en la máquina userver2 ejecutamos el comando (Dejando un espacio en blanco para la paraphrase):

*guillesiesta@guillesiesta:~$* ssh-keygen -t dsa

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica2/img/c.png)

Una vez creada la clave, con el siguiente comando la copiamos a la máquina principal (userver1).

*guillesiesta@guillesiesta:~$* sudo ssh-copy-id -i /root/.ssh/id_dsa.pub root@ userver1

Ahora, comprobamos que la clave se ha copiado correctamente en la máquina principal. Conectamos por ssh a userver1.

*guillesiesta@guillesiesta:~$* ssh userver1 -l root

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica2/img/ssh_keygen_userver2.png)

##Programar tareas con crontab##

Se introduce la siguiente línea en el archivo /etc/crontab de userver2 para que cada minuto haga una
copia de /var/www/ de userver1 en /var/www/ de userver2:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica2/img/crontab_linea.png)
