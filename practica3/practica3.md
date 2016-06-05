#Práctica 3. Balanceo de carga#

En esta práctica configuraremos una red entre varias máquinas de forma que tengamos un balanceador (por software) que reparta la carga entre varios servidores finales.

Antes de nada, creamos otra máquina virtual, que actuará como balanceador. En esta máquina instalaremos el servidor web nginx (que puede actuar también como balanceador de carga) y, el balanceador de carga haproxy. 

#nginx#

##Instalación##
Para instalar nginx seguimos los siguientes pasos:

*guillesiesta@guillesiesta:~$* cd /tmp/

*guillesiesta@guillesiesta:~$* sudo wget http://nginx.org/keys/nginx_signing.key

*guillesiesta@guillesiesta:~$* sudo apt-key add /tmp/nginx_signing.key

*guillesiesta@guillesiesta:~$* rm -f /tmp/nginx_signing.key

A continuación:

*guillesiesta@guillesiesta:~$* sudo echo "deb http://nginx.org/packages/ubuntu/ lucid nginx" >> sudo /etc/apt/sources.list

*guillesiesta@guillesiesta:~$* sudo echo "deb-src http://nginx.org/packages/ubuntu/ lucid nginx" >> sudo /etc/apt/sources.list

Finalmente, instalamos el paquete nginx:

*guillesiesta@guillesiesta:~$* sudo apt-get update

*guillesiesta@guillesiesta:~$* sudo apt-get install nginx

##Balancear carga con nginx##

Nos dirigimos hacia el fichero de configuración alojado en /etc/nginx/conf.d/default.conf . En el caso de que el archivo default.conf no esté, se crea. 

Este archivo tiene que contener lo proporcionado en el guión:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica3/img/default_conf.png)

Ahora reiniciamos el servicio:

*guillesiesta@guillesiesta:~$* sudo service nginx restart

Como se puede observar, en la configuración de la imagen anterior, añadimos a *upstream apaches* el parámetro *weight* . Con esto, suponemos a la primera máquina la menos potente o más sobrecargada, así que le hemos asignado menos carga de trabajo que a la segunda (de cada tres peticiones que lleguen, la segunda máquina atiende dos y la primera atenderá una).

Comprobamos esto con el siguiente comando:

*guillesiesta@guillesiesta:~$* 172.16.168.132

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica3/img/curl_nginx.png)

#haproxy#

##Instalacion##

Procedemos a instalarlo con el siguiente comando:

*guillesiesta@guillesiesta:~$* sudo apt-get install haproxy

Una vez instalado, debemos modificar el archivo /etc/haproxy/haproxy.cfg:

*guillesiesta@guillesiesta:~$* cd /etc/haproxy/haproxy.cfg

Añadimos al archivo lo señalado en el guión.

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica3/img/haproxy_cfg.png)

##Balancear carga con haproxy##

Para comenzar a usar haproxy, tenemos que liberar el puerto 80. Para eso realizaremos los siguientes pasos:

*guillesiesta@guillesiesta:~$* sudo netstat -lpn | grep :80

Nos aparecerá el proceso que está usando el puerto, ahora solamente tenemos que matarlo con *kill*:

*guillesiesta@guillesiesta:~$* sudo kill *id_proceso*

Y ya tendremos liberado el puerto 80.

Procedemos pues, a lanzar el servicio haproxy:

*guillesiesta@guillesiesta:~$* sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica3/img/lanzar_haproxy.png)

Finalmente, introducimos el siguiente comando las veces que sean necesarias para comprobar que balancea correctamente.

*guillesiesta@guillesiesta:~$* 172.16.168.132

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica3/img/curl_haproxy.png)