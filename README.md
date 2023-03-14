#   Índice

[1.Introducción al proyecto](#1)

[2.Configuración del VagrantFile y los scripts de aprovisionamiento.](#2)

[3.Configuración máquina MySQL.](#3)

[4.Configuración máquina NFS.](#4)

[5.Configuración máquinas Nginx.](#5)

[6.Configuración Balanceador.](#6)

[7.Comprobación de la instalación.](#7)

<div id="1">

# 1.Introducción al proyecto

El proyecto que vas a ver a continuación es una estructura CMS, la cual cuenta con 1 balanceador, mediante el cual vamos a acceder a la estructura; 2 servidores nginx los cuales nos van a hacer de puente para conectarnos al servidor mysql en el que tenemos nuestra base de datos; 1 máquina con un servidor nfs con el cual compartiremos una carpeta, en la cual encontramos los recursos necesarios para levantar nuestra página web y por último tenemos el servidor mysql en el que tendremos nuestra base de datos.

<div id="2">

# 2.Configuración VagrantFile y scripts de aprovisionamiento.

Aqui esta la configuración del archivo **VagrantFile** con el cual levantaremos el entorno.
En este apreciamos que el balanceador esta en una subred aislada junto con los dos nginx, por otro lado encontramos el nfs el cual tmb se encuentra en otra subred junto con los dos nginx, y para acabar encontramos el mysql en otra distinta con los dos nginx.

### Configuración del **Balanceador** en el Vagrantfile + **script**

![](fotos/Captura1.PNG)
![](fotos/Captura2.PNG)

### Configuración del **Nginx** en el Vagrantfile + **script**

![](fotos/Captura3.PNG) 
![](fotos/Captura4.PNG)
![](fotos/Captura5.PNG)

### Configuración del **NFS** en el Vagrantfile + **script**
![](fotos/Captura6.PNG)
![](fotos/Captura9.PNG)

### Configuración del **MYSQL** en el Vagrantfile + **script**

![](fotos/Captura7.PNG)
![](fotos/Captura8.PNG)

<div id="3">

# 3.Configuración máquina MySQL.

En la máquina **MYSQL** entraremos en el directorio **/etc/mysql/mariadb.conf.d** y ahí modificaremos el archivo **50-server.cnf**.

![](fotos/Captura10.PNG)

Dentro de este cambiaremos la **bind-address** y pondremos la ip de nuestra máquina mysql **(192.168.10.1)** en lugar de **127.0.0.1**.

![](fotos/Captura11.PNG)

A continuación con el comando **sudo mysql\_secure\_installation** podremos cambiar la contraseña del **root de mysql**, este nos mostrará una sucesión de preguntas para la configuración a las que responderemos a la primera con **enter** ya que no tenemos contraseña actual de root, a las dos que sí y nos dara a escribir nuestra contraseña (en mi caso **1234**), las demas respondemos que no.

![](fotos/Captura12.PNG)

Una vez podamos haceder al mysql con el root, entramos y nos disponemos a crear el usuario el cual será el que useamos para añadir, editar y borrar datos en nuestro CMS, para ellos debemos de crearlo con **%** en vez de con **localhost** ya que al disponer de 2 nginx no podemos usar dos direcciones IP, y le daremos permisos al usuario en la base de datos que vamos a añadir más adelante.

![](fotos/Captura13.PNG)

En el home nos descargaremos el repositorio en el que tenemos la base de datos.

![](fotos/Captura14.PNG)

Entramos en el directorio que se nos ha creado y dentro de este en el directorio llamado **db**, una vez dentro procederemos a importar el fichero **database.sql**.

![](fotos/Captura15.PNG)

<div id="4">

# 4.Configuración del servidor NFS.

Lo primero de todo será crear la carpeta **carpcomp** en cada directorio **/var/www** de las 3 máquinas y montarlas con el NFS para que con descargar algo en la  máquina NFS lo tengamos también en las máquinas Nginx.

Una vez creadas debemos de configurar el archivo **/etc/exports** con las rutas de las carpetas de las máquinas Nginx y las IPs de esas máquinas, importante que sean las IPs que estan en la subred del NFS.

![](fotos/Captura16.PNG)

Tras editar el archivo reiniciamos el servicio del NFS con **sudo systemctl restart nfs-kernel-server**

Procedemos a cambiar los permisos de la carpeta **carpcomp** primero con un **sudo chmod 777 -R /var/www/carpcomp** y luego un **chown nobody:nogroup /var/www/carpcomp**.

![](fotos/Captura30.PNG)

Ahora descargaremos el **prestashop** con el comando **"sudo wget https://download.prestashop.com/download/releases/prestashop_1.7.7.5.zip"** y lo descomprimimos con **"sudo unzip prestashop_1.7.7.5.zip"**

![](fotos/Captura26.PNG)

![](fotos/Captura27.PNG)

Ahora editaremos el archivo **/etc/php/7.4/fpm/pool.d/www.conf** y lo configuramos para poder conectarnos por socket TCP/IP. Aquí cambiaremos el contenido de la línea listen por la dirección **0.0.0.0:9000**.

![](fotos/Captura21.PNG)

<div id="5">

# 5.Configuración máquinas Nginx.

Ahora en las máquinas Nginx editaremos **/etc/nginx/sites-enabled/default**. Dentro de este en la linea que pone root, pondremos la ruta de nuestra carpeta nfs de **/var/www/carpcomp** y mas abajo agregaremos index.php después de index.html. 

![](fotos/Captura22.PNG)

Mas abajo en el mismo archivo habrá unas líneas que empiezan por location comentadas, las descomentaremos, pero solo las que salen en la captura y en la otra línea de **fatscgi_pass** pondremos la IP de nuestro servidor NFS y el puerto 9000.

![](fotos/Captura23.PNG)

Montaremos las carpetas de las máquinas Nginx con el comando **sudo mount 172.16.1.40:/var/www/carpcomp /var/www/carpcomp**. Siendo la IP de nuestra máquina NFS y las carpetas compartidas de ambas máquinas.

![](fotos/Captura17.PNG)

![](fotos/Captura18.PNG)

Por último con el comando **df -h** comprobamos que las rutas se han creado correctamente:

![](fotos/Captura31.PNG)

**EXTRA**: Si quieres configurar estas para que cada vez quen se arranquen las máquinas no tengas que repetir el comando en el archivo **/etc/fstab** ponemos el siguiente comando:

![](fotos/Captura32.PNG)

<div id="6">

# 6.Configuración Balanceador.

Creamos el archivo **/etc/nginx/sites-available/confbalancer** y dentro copiamos la configuración de la siguiene captura, remplazando las IPs por las de las máquinas nginx (las IPs de la subred del balancer **172.16.1.20 y x.30**).

![](fotos/Captura24.PNG)

Una vez hecho esto creamos un enlace hacia sites-enabled y borraremos el default.

![](fotos/Captura25.PNG)

<div id="7">

# 7.Comprobacion de la instalación.

Buscaremos en el navegador la dirección pública de nuestra máquina **balancer** en mi caso es la 192.168.1.132, pero de la siguiente forma **"http://192.168.1.132"** y nos aparecera lo siguiente, mostrando que hemos realizado bien la configuración y la instalación se encuentra preparada para realizarse.

![](fotos/Captura28.PNG)

![](fotos/Captura29.PNG)
