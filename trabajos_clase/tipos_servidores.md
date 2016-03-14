# Trabajo 1
## Tipos de servidores y sus tipos de contenido 

**Apache**

Es el más usado en servidores web. Aunque esto no quiere decir que sea el mejor. Su gran deficiencia es el rendimiento.

Apache crea hilos y sub-hilos para manejar conexiones adicionales. El administrador puede configurar el servidor para controlar el número máximo de hilos, pero esto depende de la memoria disponible en la máquina. Una vez se llegue al máximo de hilos alcanzados, Apache restringe las conexiones adicionales. 

Esto hace que demasiados procesos en memoria sean perjudiciales para el rendimiento de la máquina. 

**nginx**

Su arquitectura es distinta a la de Apache, nginx no crea una instancia por cada petición. Se puede utilizar com proxy inverso, esto quiere decir que se utiliza para equilibrar la carga entre los servidores back-end, como también para ser utilizado como caché de un servidor back-end lento. 

Está compuesta por módulos que se incluyen en tiempo de compilación. 

Empresas como Facebook y WordPress lo utilizan. Además de tumblr, Instagram, Yahoo, YouTube, Pinterest, Zynga, SourceForge, GitHub, DropBox, Intel, NetFlix, entre otras.

**thttpd**

Es un servidor web ligero. No proporciona toda la funcionalidad de Apache pero contiene lo necesario para servir grandes volúmenes de información estática.

Utiliza los requerimientos mínimos de un servidor HTTP. Es un servidor simple, pequeño, portátil (se compila en la mayoría de los sistemas operativos), rápido y seguro.

Este servidor es usado para obtener más velocidad a la hora de transmitir archivos, reduciendo los gastos innecesarios para funciones que no son requeridas por el servidor. 

**Cherokee**

Es mucho más rápido que Apache, tanto para servir contenido dinámico como estático. Es fácil de configurar, ya que tiene un panel de control. Además, sirve como balanceador de carga entre servidores.

Soporta las siguientes tecnologías web: FastCGI, SCGI, PHP, uWSGI, SSI, CGI, LDAP, TLS/SSL, HTTP proxying, video streaming, content caching, traffic shaping, etc.

**node.js**

Creado por Google. Todas las ejecuciones están formadas por un solo hilo de proceso, logrando una mayor velocidad de respuesta. Es capaz de manejar múltiples peticiones, es un servidor para usarlo en aplicaciones que demanden datos en tiempo real para un gran número de visitantes, como un sistema de noticias o un chat. 
