ruta hosts:
```
C:\Windows\System32\drivers\etc
```
Configuración Vagrant file
```
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"

  config.vm.network "forwarded_port", guest: 80, host: 8000
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  
end
```
## PASO 1: Poner en marcha gunicorn con un entorno virtual
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
## PASO 2: Conectar como un proxy gunicorn con Apache
Para hacerlo proxy:
Habilito los módulos proxy
```
sudo a2enmod proxy proxy_http
```
Creo un virtual host con la directriz ProxyPass, indicando que todo lo que vaya a la raíz lo coja apache del puerto 8080 que es donde sirve gunicorn
```
<VirtualHost *:80>
        ServerName temperaturas.local

        ServerAdmin webmaster@localhost
        DocumentRoot /home/vagrant/flask_temperaturas/
        ProxyPass / http://localhost:8080/

</VirtualHost>
```
Habilito el sitio y reinicio apache
```
sudo a2ensite /etc/apache2/sites.available/temperaturas.conf
sudo service apache2 reload
```
Compruebo funcionamiento en la dirección
```
http://temperaturas.local:8000/
```
Cambio la configuración de vagrant y reinicio la máquina virtual para que todos los accesos sean a través de apache
  ```
  config.vm.network "forwarded_port", guest: 80, host: 8000
  #  config.vm.network "forwarded_port", guest: 8080, host: 8080
```
Vuelvo a lanzar vagrant y me conecto a la máquina
Activo nuevamente el entorno virtual y lanzo gunicorn por el puerto 8080
```
source env/bin/activate
gunicorn app:app -b :8080
```
Edito el sitio temperaturas.conf para que los datos de los estáticos no los sirva gunicorn y los sirva directamente apache añadiendo la directriz:
```
ProxyPass /static/ !
Alias /static/ /home/vagrant/flask_temperaturas/static
```
También hay que dar permisos al directorio
```
<Directory /home/vagrant/flask_temperaturas/static>
    Require all granted
</Directory>
```
El virtualhost queda de la siguiente manera:
```
<VirtualHost *:80>
        ServerName temperaturas.local

        ServerAdmin webmaster@localhost
        DocumentRoot /home/vagrant/flask_temperaturas/
        
        ProxyPass /static/ !
        ProxyPass / http://localhost:8080/
        Alias /static/ /home/vagrant/flask_temperaturas/static

        <Directory /home/vagrant/flask_temperaturas/static>
            Require all granted
        </Directory>
</VirtualHost>
```
Reinicio apache
```
sudo systemctl restart apache2
```
## PASO 3: Programarlo como un servicio
En el directorio donde están todos los servicios:
```
/etc/systemd/system/
```
Creo uno nuevo servicio llamado temperaturas.service con la siguiente configuración:
```
Description=gunicorn daemon
[Service]
User=vagrant
Group=vagrant
WorkingDirectory=/home/vagrant/flask_temperaturas/

ExecStart=/home/vagrant/flask_temperaturas/env/bin/gunicorn \
    --workers=4 \
    --bind=127.0.0.1:8080 \
    --log-file=/home/vagrant/flask_temperaturas/gunicorn.log \
    app:app
    
[Install]
WantedBy=multi-user.target
```
Habilito el servicio creado y lo inicio
```
sudo systemctl enable temperaturas
sudo systemctl start temperaturas
```
Salgo de la máquina virtual y la vuelvo a iniciar para comprobar que funciona
