#Práctica 4. Comprobar el rendimiento de servidores web

##Comprobar el rendimiento de servidores con Apache Benchmark

##Máquina 2 con nginx

En primer lugar, voy a comenzar comprobando el rendimiento con Apache Benchmark de una de nuestras máquinas servidoras. En mi caso, de mi máquina 2, haciendo peticiones IP.

Primero, creo un pequeño documento html para en la máquina 2 (en el directorio /var/www), tal y como muestro en la siguiente imagen:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/prueba_hmtl.png)

Ahora, desde el sistema operativo anfitrión (Ubuntu 14.04) abro una terminal y tecleo el comando:

*guillesiesta@guillesiesta:~$* ab -n 1000 -c 5 http://172.16.68.131/prueba.html

Tomo 10 mediciones, y me quedo con "Time taken for tests", "Failed requests", "Requests per second" y "Time per request". Como resultado obtengo la siguiente tabla y sus correspondientes diagramas:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_nginx.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_maquina2_ttft.png)


![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_maquina2_rps.png)

##Balanceador con nginx

Previamente, en la máquina 1 también añado en el directorio /var/www/ el archivo html anterior. Una vez hecho eso, ejecuto el siguiente comando, tomando la ip destino, la ip del balanceador.

*guillesiesta@guillesiesta:~$* ab -n 1000 -c 5 http://172.16.68.133/prueba.html

Como resultado, obtengo la siguiente tabla y sus correspondientes diagramas.

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_nginx.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_nginx_fr.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_nginx_rps.png)

##Balanceador con haproxy

Ahora, desactivo nginx y activo haproxy:

*guillesiesta@guillesiesta:~$* sudo netstat -lpn | grep :80

Obtengo el pid del proceso que ejecuta ese puerto. Después, mato el proceso:

*guillesiesta@guillesiesta:~$* sudo kill "pid_proceso"

Ahora, arranco haproxy:

*guillesiesta@guillesiesta:~$* sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg

Comienzo enviando peticiones con el siguiente comando:

*guillesiesta@guillesiesta:~$* ab -n 1000 -c 5 http://172.16.68.133/prueba.html

Como resultado de las mediciones, obtengo la siguiente tabla y sus correspondientes diagramas:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_haproxy.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_haproxy_ttft.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_haproxy_fr.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/ab_balanceador_haproxy_rps.png)

##Comprobar el rendimiento de servidores con Siege

Con el siguiente comando, voy tomando las correspondientes medidas. Tendré en cuenta "Avaliability", "Elapsed Time", "Response Time", "Transaction rate" y "Longest transaction". Como resultado, obtengo la siguiente tabla y sus correspondientes diagramas:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/siege_balanceador.jpg)


![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/siege_availability.png)


![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/siege_elapsed_time.png)


![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/siege_transaction_rate.png)


![img](https://github.com/guillesiesta/swap_1516/blob/master/practica4/img/siege_longest_transaction.png)