Práctica 5
======

Introducción
--------------

En esta práctica vamos a aprender a crear ficheros de respaldo de bases de datos y a crear un servidor de base de datos para tener replicados todos los datos y así tener copias actualizadas en cada momento.

Para desarrollas esta práctica he creado 2 nuevas máquinas virtuales, llamadas "DB1 Master" y "DB2 Slave". En ambas he instalado Ubuntu Server 12.04 con LAMP (necesitaba MySQL).   

Parte 1: Creación de una Base de Datos nueva.
-------------------------------------------------

Voy a explicar como crear una base de datos nueva en nuestro servidor de bases de datos MySQL.

En primer lugar accedemos al servicio MySQL mediante el comando:

`mysql -u root -p`  

Ahora tenemos que crear una nueva Base de Datos, que yo en mi caso llamaré "contactos" mediante le siguiente comando;

`create database contactos;  `

Para estar seguros de que se ha creado correctamente podemos usar el comando;

`show databases;`

Y veremos algo como esto:

IMG basesdedatos


Ahora creamos una tabla en nuestra base de datos;

`create table datos (nombre varchar(100), tlf int);`

Y podemos insertar una tupla en dicha tabla;

insert into datos (nombre, tlf) values ("Javi", 686533791);

Ahora para ver que todo esta correcto podemos hacer una consulta mediante;

`select * from datos;`

IMG insert


Parte 2: Replicación manual de la estructura y los datos.
-----------------------------------------------------------

En esta parte vamos a ver como hacer una replicación manual de los datos mediante un fichero que primero tendremos que crear en el Master y luego llevarlo al Slave para importarlo.

En primer lugar y por seguradad, es recomendable bloquar nuestras tablas para que sean solo lectura (por si hubiera trafico durante el proceso, para eso accedemos a mysql y ponemos este código;

`FLUSH TABLES WITH READ LOCK;`

`quit;`

Ahora ya estan bloqueadas las tablas para solo lectura y podemos proceder con la replicación manual.   En primer lugar lanzamos el siguiente comando desde el terminal, con permisos de root:

`mysqldump contactos -u root -p > /root/contactos.sql`

IMG mysqldump1

Ahora tenemos que irnos a la máquina DB2 Slave, donde nuestra intención es importar el fichero creado en el Master. Para hacer esto, primero tenemos que tener ssh instalado en nuestras máquinas, usando `apt-get install openssh` por ejemplo, ahora desde la máquina DB2 Slave, y como root escribimos: 

` scp root@maquina1:/root/contactos.sql /root/   `  

En mi caso ha quedado como vemos ne la captura:

IMG scp

Una vez esto hecho, hay que crear una base de datos nueva y vacía en DB2 Slave:

`create database contactos;`

Salimos de MySQL y en el terminal escribimos lo siguiente para importar la información del fichero capturado anteriormente a nuestra base de datos recien creada;

`mysql -u root -p contactos < /root/contactos.sql`  

Una vez esto hecho ya tenems la información perfectamente replicada en ambas máquinas, pero el proceso no es automático, para que lo sea, tenemos que realizar la tercera parte de esta práctica:   


Parte 3: Replicación automática mediante Master-Slave
-----------------------------------------------------------


