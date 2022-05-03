En /home/vagrant/ clono el repositorio
```
$ git clone https://github.com/josedom24/flask_temperaturas.git
```
Instalo el paquete python3.8-venv
```
sudo apt install python3.8-venv
```
Preparo la máquina virtual para entornos de python
En el directorio del proyecto clonado /home/vagrant/flask_temperaturas:
Creo un entorno virtual
```
python3 -m venv env
```
Activo el entorno virtual
```
source env/bin/activate
```
Instalo las dependencias
```
pip install flask lxml
```
Instalo el servidor web específico
```
pip install gunicorn
```
Activo la aplicación en el servidor gunicorn y le indico que lo saque por el puerto 8080
```
gunicorn app:app -b :8080
```
Habilito los módulos proxy
```
sudo a2enmod proxy proxy_http
```
Creo un virtual host
```
<VirtualHost *:80>
        ServerName temperaturas.local

        ServerAdmin webmaster@localhost
        DocumentRoot /home/vagrant/flask_temperaturas/
        ProxyPass / http://localhost:8080/

</VirtualHost>
```


Instala y configura el módulo wsgi para apache2
```
apt install libapache2-mod-wsgi-py3
```
