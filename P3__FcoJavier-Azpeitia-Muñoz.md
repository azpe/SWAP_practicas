Práctica 3
======
Pasos seguidos para el desarrollo de la práctica:  
**1**--Creación de una nueva máquina virtual llamada "Ubuntu Server 3 Balanceador"
	Mediante VMware Player.	

**2**--Instalación de Ubuntu Server 12.0.4 sin LAMP en la nueva máquina.
	El mismo SO que en las máquinas de las prácticas previas pero SIN
	LAMP


nginx
------


**3**--Instalción de nginx en la nueva máquina  
	Varios pasos a seguir para instalar correctamente nginx:  
	Nos movemos al directorio temporal  
`cd /tmp/ `  
                               
Solicitamos la key del repo de nginx    
`wget http://nginx.org/nginx_signing.key`  

añadimos la key a la lista de apt  
	`apt-key add /tmp/nginx_signing.key	`  
	
Eliminamos la key de nginx de nuestro directorio temp  
`rm -f /tmp/nginx_signing.key	`  
	Ahora tenemos que añadir el repositorio donde está nginx, para esto añadí 2 lineas en 
		el fichero de repositorios de apt   
`sudo vim /etc/apt/sources.list`  

Añadimos:    

    echo "deb http://nginx.org/packages/ubuntu/ lucid nginx" >> /etc/apt/sources.list  
    echo "deb-src http://nginx.org/packages/ubuntu/ lucid nginx" >> /etc/apt/sources.list   

Actualizamos apt mediante:  
	`sudo apt-get update`	//Esta operación puede tardar bastantes minutos, paciencia.  
	
Finalmente instalamos nginx   
	`sudo apt-get install nginx`  
	
	
**4**--Configuración del fichero de configuración de nginx en la nueva máquina  
	Ya que nginx no fué ideado para ser una herramienta balanceadora por defeco, si no
	que fué diseñada como un servidor web tenemos que configurarlo y que haga la tarea 
	de balanceador. Para esto modificamos el siguiente fichero: (Si no existe, lo creamos)  
/etc/nginx/config.d/default.conf   

Añadimos lo siguiente:  
    
    upstream apaches {
     	server 192.168.136.129; //Servidor con apache "Ubuntu Server 2"
     	server 195.168.136.128;	//Servidor con apache "Ubuntu Server 1"
    }
    server{
    listen 80;	//Puerto de escucha, http usa el 80
    server_name Mycosa;	//Nombre del servidor
    access_log /var/log/nginx/Mycosa.access.log;
     error_log /var/log/nginx/Mycosa.error.log;
    root /var/www/;
     location /
     {
     	proxy_pass http://apaches;	//Aquí es donde se convierte en balanceador
     	proxy_set_header Host $host;
    	proxy_set_header X-Real-IP $remote_addr;
     	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     	proxy_http_version 1.1;
     	proxy_set_header Connection "";
     	}
    }
    	

**5**--Puesta en marcha de la tercera máquina con funcionalidad balanceadora
de las 2 máquinas virtuales "Ubuntu Server 1" y "Ubuntu Server 2"
que si tienen Apache funcionando y sirvierndo páginas web. 

    sudo service nginx restart
    
**6**--Comprobación de que funciona correctamente la máquina balanceadora pidiendole
paginas web y viendo que las sirve sin tener ella misma apache, pero si 
las máquinas que balancea.  

Mediante un navegador (Chrome) en la máquina anfitriona accediendo a la dirección
IP de la máquina "Ubuntu Server 3 Balanceador", es decir 192.168.136.130, he
podido comprobar como va sirviendome la web de la máquina Ubuntu Server 1 y de 
la máquina Ubuntu Server 2, ya que puse diferentes index.html para cada una de ellas.
Al ser Round-Robin, en cada conexión me servía una web distinta alternandolas.

**7**--Cambié la forma de balancear round-robin por otra distinta usando pesos, es decir, ponderando
cada máquina mediante un peso:  

Para esto en el fichero /etc/nginx/config.d/default.conf cambiamos:  

    upstream apaches {
    	server 192.168.136.129 weight=10 	//Servidor con apache "Ubuntu Server 2"
    	server 195.168.136.128;weight=5		//Servidor con apache "Ubuntu Server 1"
    }


Aquí estoy suponiendo que la máquina primera es mas potente que la segunda ya que va a servir
mas peticiones al tener mayor peso.
	

haproxy
--------


**3**--Instalción de haproxy en la nueva máquina  


Para instalar haproxy es sificiente con poner:  

`sudo apt-get install haproxy`  
Con esta linea es sufiente para instalarlo.
	
	
**4**--Configuración del fichero de configuración de haproxy en la nueva máquina  
Para configurar haproxy como balanceador, he editado el fichero:  
/etc/haproxy/haproxy.cfg  

Y rellene el fichero con lo siguiente:
	
	global
		daemon		//lanzarlo como un Daemon
		maxconn 256	//número maximo de conexiones
	defaults
		mode http		//modo de funcionamiento
		contimeout 4000		//Tiempo máximo de expera para cada intento de conexion
		clitimeout 42000	//Tiempo máximo de inactividad del cliente
		srvtimeout 43000	//Tiempo máximo de inactividad del servidor
	frontend http-in
		bind *:80			//Se adueña del puerto 80
		default_backend servers		//Etiqueta "servers" que indica el backend por defecto
	backend servers					//Lista de servidores backends con estructura round-robin por defecto
		server m1 172.16.168.130:80 maxconn 32		//máquina 1 "m1" con número maximo de conexiones 32
		server m2 172.16.168.131:80 maxconn 32		//máquina 2 "m2" con número maximo de conexiones 32
	

**5**--Puesta en marcha de la tercera máquina con funcionalidad balanceadora
	de las 2 máquinas virtuales "Ubuntu Server 1" y "Ubuntu Server 2"
	que si tiene Apache funcionando y sirvierndo páginas web.

	sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg

**6**--Comprobación de que funciona correctamente la máquina balanceadora pidiendole
paginas web y viendo que las sirve sin tener ella misma apache, pero si 
las máquinas que balancea.  
	
Mediante un curl (`sudo apt-get install curl`)en la máquina anfitriona accediendo a la dirección
IP de la máquina "Ubuntu Server 3 Balanceador", es decir "`curl 192.168.136.130`", he
podido comprobar como va sirviendome la web de la máquina Ubuntu Server 1 y de 
la máquina Ubuntu Server 2, ya que puse diferentes index.html para cada una de ellas.   
Al ser Round-Robin, en cada conexión me servía una web distinta alternandolas.


**7**--Cambié la forma de balancear round-robin por otra distinta usando pesos, es decir, ponderando
cada máquina mediante un peso:  

Para esto en el fichero /etc/haproxy/haproxy.cfg cambiamos:  

		backend servers					//Lista de servidores backends con estructura round-robin por defecto
		server m1 172.16.168.130:80 weight 10 maxconn 32		//máquina 1 "m1" con número maximo de conexiones 32 con peso 10
		server m2 172.16.168.131:80 weight 5 maxconn 32		//máquina 2 "m2" con número maximo de conexiones 32 con peso 5  
		
Aquí estoy suponiendo que la máquina primera es mas potente que la segunda ya que va a servir
mas peticiones al tener mayor peso.
	



































	
		