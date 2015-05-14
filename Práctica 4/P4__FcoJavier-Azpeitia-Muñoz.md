Práctica 4
======

Introducción
--------------

En esta práctica voy a explicar como he realizado todos los experimentos de rendimiento del sistema creado previamente en la **práctica 3**.

Todas las pruebas se han desarrollado con el mismo hardware, la misma red y bajo las mismas condicones de carga externa. En la plataforma **Windows**, mas concretamente **Windows 7 Ultimate 64bits**.

Apache Benchmark
---------------------

Para realizar las pruebas correspondientes con **Apache Benchmark**, en mi caso lo hice en **Windows**, para lo cual es necesario tener apache instalado en la máquina, en mi caso esto estaba resuelto ya que tenia instalado **XAMPP**, el cual incluye **Apache** y a su vez **Apache Benchmark**.

Para lanzarlo hice lo siguiente:  
Abrir un terminal en Windows mediante el comando *cmd* y a continuación situarnos en el directorio donde se encuentra la herramienta *ab.exe*, tal que:  
*cd C:/xampp/apache/bin*  

Es la ruta por defecto al instalar **XAMPP**. Una vez situado aquí basta con lanzar:  
*ab -n X -c Y http://sitio.web.a.testear*  

Donde la *X* indica el número de solicitudes a realizar, y la *Y* indica el numero de solicitudes concurrentes o simultaneas que se van a realizar.

Esta es una captura tomada tras realizar una prueba a uno de los servidores finales (Ubuntu Server 1)  

![](https://raw.githubusercontent.com/azpe/SWAP_practicas/master/Pr%C3%A1ctica%204/images/ab_128_500_10.PNG)



Her resaltado las medidas mas importantes y con las cuales voy a realizar unas tablas y unas gráficas para ilustrar estos resultados.  

