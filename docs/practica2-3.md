## Natividad Márquez Baena

# Práctica 2.3 – Proxy inverso con Nginx

1. [Introducción](#1)
2. [Configuraciones](#2)
3. [Comprobaciones](#3)

<div id="1"></div>
## INTRODUCCIÓN

Un proxy es un servidor conectado a un grupo de máquinas cliente.
El servidor proxy intercepta las solicitudes de los clientes y se comunica con los servidores web actuando como intermediario de parte del cliente.
La existencia de los proxys se fundamenta en varias razones, por ejemplo, en la necesidad de evitar restricciones de navegación ante situaciones donde ecxisten restricciones de acceso a navegación web, bloquear acceso a contenido o proteger nuestra identidad en linea.

En esta practica se va a proceder a la creación de un proxy inverso, que es realmente el concepto contrario explicado, siendo un servidor intermedio que recibe la respuesta de varios servidores web y actuando como servidor principal para que ningun cliente se comunique directamente con el servidor o servidores.

Este procedimiento trae ventajas como reforzar la seguridad contra ataques, almacenamiento de caché o cifrado SSL para protección de solicitudes.

<div id="2"></div>

## CONFIGURACIONES

Se procederá a la configuración de dos máquinas Debian. Para ello previamente se ha relizará la clonación de una de ellas para obtener dos con las que trabajar. De esta manera se utilizará el servidor que ya se ha configurado previamente. El nuevo servidor Debian que se ha clonado será configurado como proxy inverso.
Para la comprobación se realizará la busqueda en un navegador de la máquina anfitriona, hacia el proxy clonado, que debería de redirigir hacia el servidor web principal.

### Nginx en servidor web

Se cambiará el nombre de la web previamente creada por webserver mediante el comando `mv`

![alt text](image-1.png)

Además se cambiará modificará el nombre de la web en el archivo de configuración. 

![alt text](image-2.png)

Posteriormente se crea un enlace simbolico llamado webserver 

![alt text](image-3.png)

Y se elimina el link simbolico que existía previamente.

![alt text](image-4.png)

Finalmente se reinicia el sistema nginx para que se apliquen los cambios realizados.

Es importante modificar en el archivo hosts de la máquina anfitriona, cambiando el nombre de la web al actual.

![alt text](image-5.png)

Se comprueba que es posible el acceso a la web tras los cambios y con el nuevo nombre actualizado.

### Nginx proxy inverso

En la máquina clonada, se desarrollará el proxy inverso, que nos redirigirá a `http://webserver:8080`, el servidor web que acabamos de configurar para que escuche con ese nombre en el puerto 8080. 

Para ello se crea un archivo se configuración en la ruta `/etc/nginx/sites-available` que se llamará ejemplo-proxy, y tendrá el siguiente contenido:

![alt text](image-6.png)

Se detallará el puerto donde está escuchando el proxy inverso(IP), el nombre del sitio web creado anteriormente con el que accedemos en el proxy, y la directiva proxy_pass, que indica donde se redigirán las peticiones

Se realiza la comprobación mediante el navegador web.

![alt text](image-9.png)

Posteriormente se creará el link simbólico pertinente.

![alt text](image-10.png)

Finalmente se reinicia el sistema para aplicar los cambios aplicados.

![alt text](image-11.png)

## COMPROBACIONES

En este apartado se pretente comprobar que la petición está pasando por el proxy inverso, que está llegando al servidor web y que vuelve por el mismo camino.
Primeramente se han de hacer las comprobaciones de que es posible acceder a la web.
Posteriormente se añadirán cabeceras HTTP personalizadas para una comprobación más exhaustiva de que las peticiones pasan tanto por el proxy inverso como por el servidor web.
Para ello se editará el archivo de configuración, tanto del webserver como del proxy inverso, añadiendo a cada uno la cabecera host. De tal manera que:

![alt text](image-12.png)
![alt text](image-13.png)

Se añade al bloque `location /` la cabecera para el webserver.

Posteriormente se hace lo mismo para el proxy inverso:

![alt text](image-14.png)
![alt text](image-15.png)

Posteriormente se reinicia el servicio de nginx para aplicar los datos, en ambas máquinas Debian.

![alt text](image-16.png)

El resultado esperado es que, al inspeccionar la web, encontremos ambas cabeceras personalizadas, que nos indicarían que la petición ha pasado por los dos lados.

Además, es importante modificar el archivo `/etc/hosts`de la máquina con el proxy inverso, añadiendo la dirección del servidor web y la IP especifica.

![alt text](image-19.png)
![alt text](image-20.png)

De esta manera, al realizar la comprobación inspeccionando la página se debería obtener el siguiente resultado **( Es importante desactivar la opción de `desactivar caché` para evitar resultados inesperados ).**

![alt text](image-17.png)
![alt text](image-18.png)



