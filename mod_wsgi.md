En /home/vagrant/ clono el repositorio
```
$ git clone https://github.com/josedom24/flask_temperaturas.git
```
Instalo el paquete python3.8-venv
```
sudo apt install python3.8-venv
```
Preparo la máquina virtual para entornos de python
En /home/vagrant:
Creo un entorno virtual
```
python3 -m venv env
```
Activo el entorno virtual
```
source env/bin/activate
```
Accedo a flask_temperaturas e instalo las dependencias
```
pip install flask lxml
```
Instalo el servidor web específico
```
pip install gunicorn
```
Instala y configura el módulo wsgi para apache2
```
apt install libapache2-mod-wsgi-py3
```
Activo la aplicación en el servidor gunicorn y le indico que lo saque por el puerto 8080
```
apt install libapache2-mod-wsgi-py3
```
