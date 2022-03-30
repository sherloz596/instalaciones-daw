### Práctica: Instalación de Wordpress
Levanto y actualizo el sistema
```
vagrant up
vagrant ssh
sudo apt update
```
Instalo el cliente de base de datos mariadb (no se instaló en la primera práctica)
```
sudo apt install mariadb-client
```
Accedo a la base de datos
```
sudo mysql -u root -p
```
Configuro la base de datos
```
create database wordpress;
use wordpress;
create user 'user'@'localhost';
grant all privileges on wordpress.* to 'user'@'localhost' identified by 'user';
flush privileges;
```
Copio la ruta de descarga desde la página de descarga de wordpress y lo descargo en el documentRoot (/var/www/html) con la herramienta wget
```
sudo wget https://es.wordpress.org/latest-es_ES.zip
```
Una vez descargado instalo unzip y lo descomprimo
```
sudo apt install unzip
sudo unzip latest-es_ES.zip
```
Hago propietario del documentRoot al usuario de apache www-data para que pueda grabar el fichero de configuración de la instalación directamente en el documentRoot
```
sudo chown -R www-data:www-data html/
```
Accedo al enlace de instalación
```
http://localhost:8080/wordpress/wp-admin/setup-config.php
```
Introduzco los detalles de la conexión
```
Nombre de la base de datos: wordpress
Nombre del usuario: user
Contraseña: user
Servidor de la base de datos: localhost
Prefijo de tabla: wp_
```
Ejecuto la instalación y relleno la información que solicita:
```
Título del sitio: Despliegue-tema4
Nombre de usuario: admin
Contraseña: adminDAW004
Tu correo electrónico: admin@exam.es
```
Hago login con las credenciales introducidas y accedo al panel de administración:

Accedo a wordpress desde http://localhost:8080/wordpress
