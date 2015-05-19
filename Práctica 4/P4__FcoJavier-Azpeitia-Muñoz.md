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

Tablas:
=====

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/ab_table_finalserver1.png?raw=true)

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/ab_table_nginx.png?raw=true)

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/ab_table_haproxy.png?raw=true)


Gráficas:
======

Servidor final:
----------------
![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/ab_graf_finalserver1.png?raw=true)

Balanceador nginx:
----------------

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/ab_graf_nginx.png?raw=true)

Balanceador haproxy:
----------------

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/ab_graf_haproxy.png?raw=true)   




**Fin de las pruebas con Apache Benchmark.**    



Siege.
====

Al igual que con Apache Benchmark voy a probar Siege en Windows, para ello, en primer lugar descargé Siege para Windows de esta URL:
[Siege Download](https://code.google.com/p/siege-windows/)  

En esa misma dirección podemos encontrar varios ejemplos de como se usa Siege desde un SO Windows.

A continuación muestro una captura de pantalla de como muestra Siege los resultados despues de realizar las pruebas:

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_screenshot_resultado.PNG?raw=true)


Las partes marcadas en azul son las mas importantes a la hora de hacer un análisis de prestaciones.  

 
En el caso de Siege, en su ejecución tenemos que darle como parámetros tanto la concurrencia de las peticiones como el tiempo que durará el test, y dado que ya hice el test con Apache Benchmark y tengo algunos tiempos orientativos, voy a hacer uso de ellos.  

En las siguientes tablas quedan representados los resultados obtenidos al usar Siege sobre el mismo sistema web.

Tablas:
=====


![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_table_finalserver1.png?raw=true)  

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_table_nginx.png?raw=true)  

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_table_haproxy.png?raw=true)  


Gráficas:  
======

Servidor final:
---------------

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_graf_finalserver1.png?raw=true)


Balanceador nginx:
---------------------

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_graf_nginx.png?raw=true)


Balanceador haproxy:
-----------------------

![](https://github.com/azpe/SWAP_practicas/blob/master/Pr%C3%A1ctica%204/images/sg_graf_haproxy.png?raw=true)



Conclusiones:
==========

Tras realizar todas las baterías de pruebas tanto con Apache Benchmark como con Siege, quisiera remarcar algunos detalles que me han parecido relevantes, teniendo en cuenta que no hay una relación entre ambos benchmarks, es decir, no miden de la misma forma, por lo cual no podemos realizar comparaciones directas entre ambos, pero si podemos remarcar detalles importantes.  

Por ejemplo, en el caso de Apache Benchmark tenemos que siempre hay peticiones fallidas mientras que con Siege nunca hay peticiones fallidas, esto es debido a que al medir de distinta forma, en caso de Siege siempre se completan las peticiones mientras que en AB no siempre se completan y las da por fallidas.

Otro detalle a tener en cuenta es que en AB la concurrencia no afecta demasiado en los resultados obtenidos, mientras que en Siege, cuando se usa un balanceador los resultados son bastante diferentes según que concurrencia establezcamos.














