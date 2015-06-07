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

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/basesdedatos.PNG?raw=true)


Ahora creamos una tabla en nuestra base de datos;

`create table datos (nombre varchar(100), tlf int);`

Y podemos insertar una tupla en dicha tabla;

insert into datos (nombre, tlf) values ("Javi", 686533791);

Ahora para ver que todo esta correcto podemos hacer una consulta mediante;

`select * from datos;`

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/insert.PNG?raw=true)


Parte 2: Replicación manual de la estructura y los datos.
-----------------------------------------------------------

En esta parte vamos a ver como hacer una replicación manual de los datos mediante un fichero que primero tendremos que crear en el Master y luego llevarlo al Slave para importarlo.

En primer lugar y por seguradad, es recomendable bloquar nuestras tablas para que sean solo lectura (por si hubiera trafico durante el proceso, para eso accedemos a mysql y ponemos este código;

`FLUSH TABLES WITH READ LOCK;`

`quit;`

Ahora ya estan bloqueadas las tablas para solo lectura y podemos proceder con la replicación manual.   En primer lugar lanzamos el siguiente comando desde el terminal, con permisos de root:

`mysqldump contactos -u root -p > /root/contactos.sql`

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/mysqldump1.PNG?raw=true)

Ahora tenemos que irnos a la máquina DB2 Slave, donde nuestra intención es importar el fichero creado en el Master. Para hacer esto, primero tenemos que tener ssh instalado en nuestras máquinas, usando `apt-get install openssh` por ejemplo, ahora desde la máquina DB2 Slave, y como root escribimos: 

` scp root@maquina1:/root/contactos.sql /root/   `  

En mi caso ha quedado como vemos ne la captura:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/scp.PNG?raw=true)

Una vez esto hecho, hay que crear una base de datos nueva y vacía en DB2 Slave:

`create database contactos;`

Salimos de MySQL y en el terminal escribimos lo siguiente para importar la información del fichero capturado anteriormente a nuestra base de datos recien creada;

`mysql -u root -p contactos < /root/contactos.sql`  

Una vez esto hecho ya tenemos la información perfectamente replicada en ambas máquinas, pero el proceso no es automático, para que lo sea, tenemos que realizar la tercera parte de esta práctica:   


Parte 3: Replicación automática mediante Master-Slave
-----------------------------------------------------------

En esta última parte de la práctica, el objetivo es conseguir que el servidor DB2 Slave copie cada cambio que se realice en el DB1 Master de forma autónoma y segura. Para hacer esto, en primer lugar configuraremos el Maestro.  

Abrimos el fichero /etc/mysql/my.cnf como root y hacemos lo siguientes cambios:

Comentamos la siguiente linea:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config1.png?raw=true)

Agregamos una linea para que cree logs de errores donde nosotros queremos;

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config2.png?raw=true)

Agregamos una linea para identificar este server como server-id = 1

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config3.png?raw=true)

Añadimos una linea para el registro binario donde estan todas las actualizaciones:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config4.png?raw=true)

Reiniciamos el servicio mediante:

`/etc/init.d/mysql restart`

Ahora vamos a configurar el DB2 Slave
 
Agregamos una linea para identificar este server como server-id = 2

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config5.png?raw=true)

Ya tenemos el Slave configurado, ahora volvemos al Master para crear el usuario que usará el Slave para hacer la replicaciones:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config7.PNG?raw=true)

Al final de la imagen anterior, vemos que se mostró la información del master que ahora usaremos en el Slave para establecer la conexión con el Master:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config8.PNG?raw=true)

Ya tenemos todo configurado y listo para funcionar, para probar si funciona correctamente, basta con hacer una inserción en el Master y ver si sale en el Slave, por ejemplo esta inserción:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config9.PNG?raw=true)

Para ver que funciona, hice una consulta antes de la inserción del Master, y después de la insercion del Master, volví a lanzar la misma consulta, y como vemos se ha actualizado de forma totalmente automática.

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%205/imagenes/config10.PNG?raw=true)

Si quisiéramos agregar mas Slaves a nuestro sistema, el proceso sería el mismo con al diferencia de que el server-id ha de ser diferente para cada server.




 
   


























