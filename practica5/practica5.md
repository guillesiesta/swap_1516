#Práctica 5. Replicación de bases de datos MySQL#

##Crear una BD e insertar datos##

En primer lugar, en la máquina 1 que será la que voy a usar como principal:

*guillesiesta@guillesiesta:~$* mysql -u root -p

Si tenemos contraseña la escribismos, sino, lo dejamos vacío y pulsamos "Enter". Ya estaremos dentro de mysql.

Introducimos los siguientes comandos para crear la base de datos "contactos":

*mysql*> create database contactos;

Usamos la BD creada:

*mysql*> use contactos;

Vemos que no hay tablas creadas:

*mysql*> show tables;

Creamos una nueva tabla:

*mysql*> create table datos(nombre varchar(100), tlf int);

Mostramos de nuevo las tablas creadas, y vemos que está la tabla "datos":

*mysql*> show tables;

Procedemos a insertar datos:

*mysql*> insert into datos(nombre,tlf) values ("guille",666123123);

*mysql*> insert into datos(nombre,tlf) values ("david",123456789);

*mysql*> insert into datos(nombre,tlf) values ("ivan",987654321);

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql1.png)

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql2.png)

Si queremos comprobar los valores tecleamos:

*mysql*> describe datos;

Salimos de mysql con:

*mysql*> quit

##Replicar una BD MySQL con mysqldump##

Primero, tenemos que tener en cuenta que los datos se pueden estar actualizando constantemente en el servidor de BD principal. En este caso, antes de hacer la copia de seguridad en el archivo .SQL debemos evitar que se acceda a la BD para cambiar nada. Para ello, accedemos primero a mysql:

*guillesiesta@guillesiesta:~$* mysql -u root -p

Y ahora introducimos:

*mysql*> FLUSH TABLES WITH READ LOCK;

*mysql*> quit

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql3.png)

Ahora ya sí que podemos hacer el mysqldump para guardar los datos. En el servidor principal (máquina 1) hacemos:


*guillesiesta@guillesiesta:~$*sudo su
 

*guillesiesta@guillesiesta:~$*mysqldump contactos -u root -p > /root/contactos.sql
 
![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql4.png)

Como habíamos bloqueado las tablas, debemos desbloquearlas (quitar el “LOCK”):

*guillesiesta@guillesiesta:~$* mysql -u root -p

*mysql*> UNLOCK TABLES;

*mysql*> quit

##Vamos a la segunda máquina##

Ya podemos ir a la máquina esclavo (máquina 2) para copiar el archivo .SQL con todos los datos salvados desde la máquina principal (maquina1):

*guillesiesta@guillesiesta:~$* scp root@userver1:/root/contactos.sql /root/contactos.sql

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql5.png)

Es importante destacar que el archivo .SQL de copia de seguridad tiene formato de texto plano, e incluye las sentencias SQL para restaurar los datos contenidos en la BD en otra máquina. Sin embargo, la orden mysqldump no incluye en ese archivo la sentencia para crear la BD (es necesario que nosotros la creemos en la máquina secundaria en un primer paso, antes de restaurar las tablas de esa BD y los datos contenidos en éstas). Con el archivo de copia de seguridad en el esclavo ya podemos importar la BD completa en el MySQL. Para ello, en un primer paso creamos la BD:

*guillesiesta@guillesiesta:~$* mysql -u root -p

*mysql*> create database ejemplodb;

*mysql*>quit

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql6.png)

Ahora, restauramos los datos contenidos en la BD "contactos" (se van a crear las tablas en el proceso):

(Tenemos que entrar como superusuario previamente)

*guillesiesta@guillesiesta:~$* mysql -u root -p ejemplodb < /root/contactos.sql

Procedemos a comprobar que lo que hay en el interior de la BD "ejemplodb" corresponde a lo que hay en la BD "contactos".

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql7.png)

##Replicación de BD mediante una configuración maestro-esclavo ##

La opción anterior funciona perfectamente, pero es algo que realiza un operador a mano. Sin embargo, MySQL tiene la opción de configurar el demonio para hacer replicación de las BD sobre un esclavo a partir de los datos que almacena el maestro. 

Se trata de un proceso automático que resulta muy adecuado en un entorno de producción real. Implica realizar algunas configuraciones, tanto en el servidor principal como en el secundario.

Lo primero que debemos hacer es la configuración de mysql del maestro (máquina 1). Para ello editamos, como root, el /etc/mysql/my.cnf para realizar las modificaciones que se describen a continuación.

*guillesiesta@guillesiesta:~$*sudo nano /etc/mysql/my.cnf

Comentamos el parámetro bind-address que sirve para que escuche a un servidor:

*bind-address 127.0.0.1*

Le indicamos el archivo donde almacenar el log de errores. De esta forma, si por ejemplo al reiniciar el servicio cometemos algún error en el archivo de configuración, en el archivo de log nos mostrará con detalle lo sucedido:

*log_error = /var/log/mysql/error.log*

Establecemos el identificador del servidor.

*server-id = 1*

El registro binario contiene toda la información que está disponible en el registro de actualizaciones, en un formato más eficiente y de una manera que es segura para las transacciones:

*log_bin = /var/log/mysql/bin.log*

Guardamos el documento y reiniciamos el servicio:

*guillesiesta@guillesiesta:~$* sudo /etc/init.d/mysql restart

Ahora procedemos a configurar la máquina 2, que hará de esclavo, podemos pasar a hacer la configuración del mysql del esclavo (vamos a editar como root el /etc/mysql/my.cnf). 

La configuración es similar a la del maestro, con la diferencia de que el server-id en esta ocasión será 2. 

Reiniciamos el servicio en el esclavo:

*guillesiesta@guillesiesta:~$* sudo /etc/init.d/mysql restart

Si no da ningún error, habremos tenido éxito. Podemos volver al
maestro para crear un usuario y darle permisos de acceso para la replicación.

Entramos en mysql y ejecutamos las siguientes sentencias:

*mysql*> CREATE USER esclavo IDENTIFIED BY 'esclavo';

*mysql*> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%'
IDENTIFIED BY 'esclavo';

*mysql*> FLUSH PRIVILEGES;

*mysql*> FLUSH TABLES;

*mysql*> FLUSH TABLES WITH READ LOCK;

Para finalizar con la configuración en el maestro, obtenemos los datos de la base de datos que vamos a replicar para posteriormente usarlos en la configuración del esclavo:

*mysql*> SHOW MASTER STATUS;


![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql8.png)

Reiniciamos el servicio mysql:

*guillesiesta@guillesiesta:~$* sudo /etc/init.d/mysql restart

Volvemos a la máquina esclava (máquina 2). Entramos en mysql y le damos los datos del maestro. Como tenemos una versión superior a  5.5. En el entorno de mysql ejecutamos la siguiente sentencia (ojo con la IP, "master_log_file" y del "master_log_pos" del maestro):

*mysql*> CHANGE MASTER TO MASTER_HOST='172.16.68.130,
MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=501, MASTER_PORT=3306;

Por último, arrancamos el esclavo y ya está todo listo para que los demonios de MySQL de las dos máquinas repliquen automáticamente los datos que se introduzcan/modifiquen/borren en el servidor maestro:

*mysql*> START SLAVE;

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql9.png)

Ahora, podemos hacer pruebas en el maestro y deberían replicarse en el esclavo automáticamente. Por último, volvemos al maestro y volvemos a activar las tablas para que puedan
meterse nuevos datos en el maestro:

*mysql*> UNLOCK TABLES;

Ahora, si queremos asegurarnos de que todo funciona perfectamente y que el esclavo no tiene ningún problema para replicar la información, nos vamos al esclavo y con la siguiente orden:

*mysql*> SHOW SLAVE STATUS\G

Revisamos si el valor de la variable “Seconds_Behind_Master” es distinto de “null”. En ese caso, todo estará funcionando perfectamente.

A continuación vemos que el valor de la variable “Seconds_Behind_Master” es 0, por lo
que no hay ningún error y todo funciona como debe:

![img](https://github.com/guillesiesta/swap_1516/blob/master/practica5/img/mysql10.png)

Para comprobar que todo funciona, debemos ir al maestro e introducir nuevos datos a la base de datos. A continuación vamos al esclavo para revisar si la modificación se ha reflejado en la tabla modificada en el maestro.

