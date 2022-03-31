### Práctica: Instalación de Drupal
##### - Configuración previa del etorno LAMP e instalación de Wordpress
Previa a la realización de esta práctica he configurado un entorno LAMP con la práctica:
[Preparar un servidor con apache2 + php + mariadb](apa_php_mdb.md)
Además he realizado también la práctica: [Instalación de Wordpress](instalacion_wordpress.md)

##### - Descarga y configuración previa
Levanto y actualizo el sistema
```
vagrant up
vagrant ssh
sudo apt update
```
Copio la ruta de descarga  del paquete .tar.gz desde la página de descarga de drupal y lo descargo con la herramienta wget
```
wget --content-disposition https://www.drupal.org/download-latest/tar.gz
```
Extraigo el paquete que acabo de descargar directamente en la ruta que me interesa para la integración con el servicio web:
```
sudo tar xf drupal-9.X.X.tar.gz -C /var/www/
```
Como el nombre del nuevo subdirectorio creado contiene el número de versión en su nombre, creo un enlace simbólico sin números:
```
sudo ln -s /var/www/drupal-9.3.9 /var/www/drupal
```
Cambio la propiedad del directorio de instalación y de su contenido al usuario con el que corre el servicio web en Ubuntu 20.04 ya que Drupal 9 necesita escribiren él
```
sudo chown -R www-data: /var/www/drupal/
```
Creo el virtual host
```
drupal.miservidor.com
```
##### - Servicio web
Activo los módulos de apache de los que podría hacer uso Drupal
```
sudo a2enmod expires headers rewrite
```
El uso de estos módulos se realiza a través de archivos .htaccess, que no son interpretados por defecto.
Añado al archivo de configuración que crearé para hacer la aplicación navegable a través de un alias
```
sudo nano /etc/apache2/sites-available/drupal.conf
```
El contenido queda así:
```
Alias /drupal /var/www/drupal
<Directory /var/www/drupal>
        AllowOverride all
</Directory>
```
Activo la configuración
```
sudo a2ensite drupal.conf
```
Reinicio el servicio web para aplicar todos los ajustes
```
sudo systemctl restart apache2
```
##### - Base de datos
Conecto al servicio con el cliente mysql y un usuario administrador
```
sudo mysql -u root -p
```
Creo la base de datos
```
create database drupal9 charset utf8mb4 collate utf8mb4_unicode_ci;
```
Como voy a usar MariaDB creo el usuario de forma trivial
```
create user drupal9@localhost identified by 'sergio';
```
Concedo los privilegios al usuario sobre la base y cierro la conexión
```
grant all privileges on drupal9.* to drupal9@localhost;
exit
```
##### - PHP
Instalo las extensiones necesarias
```
sudo apt install -y php-apcu php-gd php-mbstring php-uploadprogress php-xml
```
##### - Instalador web
Accedo al instalador web
```
http://drupal.miservidor.com:8080/drupal
```
Sigo los pasos del instalador:
- En la página inicial del instalador web elijo el idioma de la instalación
- Selecciono el perfil de instalación. En mi caso estándar
- El instalador comprueba los requisitos para la instalación 
- Configuración de la conexión al servicio de bases de datos. Elijo el configurado anteriormente e introduzco los datos de conexión.
- Comienza el proceso de intalación
- Inicializa la base de datos, instala módulos, traducciones, etc y pide la información básica de identificación del nuevo sitio web y los datos del usuario administrador
- La instalación ultima la actualización de traducciones y presenta el nuevo sitio Drupal 9 con sesión iniciada automáticamente para el usuario administrador
##### - Post-instalación
Edito el archivo de configuración de Drupal settings.php para proteger a mi sitio contra ataques de tipo «HTTP Host Header«
```
sudo nano /var/www/drupal/sites/default/settings.php
```
Configuro la variable trusted_host_patterns:
```
...
/**
 * Trusted host configuration.
...
 * $settings['trusted_host_patterns'] = [
 *   '^www\.example\.com$',
 * ];
...
 */
...
```
Este bloque de configuración está desactivado por completo en forma de comentarios, así que realizo la configuración fuera de dicho bloque. Añado a la variable sólo aquellos dominios a los que Drupal debe responder.

En mi caso al ser un servidor Ubuntu 20.04 LTS accesible en el dominio ubuntu2004.local.lan, lo configuro así
```
...
$settings['trusted_host_patterns'] = [
        '^ubuntu2004\.local\.lan$',
];
...
```

