Práctica 1
======

Tras poner en funcionamiento las máquinas virtuales he detectado  
que no se instaló LAMP, ni tan siquiera me lo pregunto durante el  
proceso de instalación, y por eso mismo he tenido que instalar    
a mano 
cada uno de los componentes, es decir, 

* **Apache** *(apt-get install apache2*,      
* **MySQL** (apt-get install mysql-server)*
* **php** *(apt-get instal php5 libapache2-mod-php5 php5-mysql)*

Tambien he tenido que cambiar el layout del kyeboard 	  
	**`(dpkg-reconfigure console-data)`**

Al terminar estos 
pasos, todo ha ido correcto, es decir, he podido hacer  
ping entre la máquina 1 y la máquina 2, ambas tienen acceso a internet  
correctamente.  


Lanzando "apache2 -v":  
-------------------------
**`Server version_ Apache/2.2.22 (Ubuntu)
Serer built: Mar  5 2015 18:10:11`**

Lanzando "ps aux | grep apache"
---------------------------------
consultar [Imagen](https://github.com/azpe/SWAP_practicas/blob/master/p1_img1.PNG?raw=true)

He tardado algo mas en entregar la práctica por que estaba usando   
Virtualbox inicialmente,
pero he querido probar VMware Player,  
más que nada para aprender a usar otro software de virtualización.