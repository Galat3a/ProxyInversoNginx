## Proxy Inverso

# Vagrantfile

```bash 

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "256"
  end # virtualbox

  # Provisión común
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update && apt-get install -y nginx
  SHELL

  # Proxy
  config.vm.define "proxy" do |p|
    p.vm.hostname = "www.example.test"
    p.vm.network "public_network"
    p.vm.network "private_network", ip: "192.168.57.10"
  end # proxy

  # Web
  config.vm.define "web" do |w|
    w.vm.hostname = "w1.example.test"
    w.vm.network "private_network", ip: "192.168.57.11"
  end # web
end

```

<img src="./capturas/1.png">

Creamos el directorio y de damos permisos:

<img src="./capturas/2.png">

Añadimos el contenido al fichero:

<img src="./capturas/3.png">

Creamos el enlace simbolico:

```bash 

sudo ln -s /etc/nginx/sites-available/w1 /etc/nginx/sites-enabled/

```
Añadimos el contenido a index.html:

<img src="./capturas/4.png">
<img src="./capturas/5.png">

Instalamos curl y lo probamos:

<img src="./capturas/6.png">
<img src="./capturas/7.png">


# Configuración del proxy

Consulatamos la ip:

<img src="./capturas/8.png">

Una vez creado el backup del archivo default, se suprime el original y se sustituye por el siguiente:

<img src="./capturas/9.png">

Comprobamos errores y reiniciamos el servidor:

```bash 
sudo nginx -t
sudo systemctl restart nginx
```

Editamos el host:

<img src="./capturas/10.png">

Editamos el sites-enabled:

<img src="./capturas/11.png">

Editmos el hsot del anfitrion (el pc con el que vamos a navegar a la web):

<img src="./capturas/12.png">

Visitamos http://www.example.test/:

<img src="./capturas/13.png">

Para ver el trafico usamos el siguiente comando:

```bash 

vagrant ssh -c "sudo tail /var/log/nginx/access.log" web
vagrant ssh -c "sudo tail /var/log/nginx/access.log" proxy

```

<img src="./capturas/14.png">

<img src="./capturas/15.png">

# Añadir cabeceras al proxy inverso

Entramos en vagrant ssh proxy, y en sites-enabled/default añadimos:

```bash 
add_header X-friend acarmar;
```
 <img src="./capturas/17.png">

 # Añadir cabeceras al servidor web

 Entramos en vagrant ssh web, y en sites-enabled/w1 añadimos:

```bash 
add_header Host w1.example.test;
```
 <img src="./capturas/19.png">
