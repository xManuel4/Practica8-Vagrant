# Vagrantfile
Vagrant.configure("2") do |config|
    # Configuración para el servidor Apache 1
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/jammy64"
      web1.vm.network "private_network", ip: "192.168.56.11"
      web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Directorio compartido para editar archivos de Apache localmente
      web1.vm.synced_folder "src", "/var/www/html"
      # Instalar Apache en web1
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo echo "<h1>Hola Devops - esto es una prueba de load balancing. Soy el servidor #1</h1>" > /var/www/html/index.html
      SHELL
    end
  
    # Configuración para el servidor Apache 2
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/jammy64"
      web2.vm.network "private_network", ip: "192.168.56.12"
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Directorio compartido para editar archivos de Apache localmente
      web2.vm.synced_folder "src", "/var/www/html"
      # Instalar Apache en web2
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo echo "<h1>Hola Devops, esto es una prueba de load balancing. Soy el servidor Apache #2</h1>" > /var/www/html/index.html
      SHELL
    end
  
    # Configuración para el servidor Nginx
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/jammy64"
      nginx.vm.network "private_network", ip: "192.168.56.13"
      nginx.vm.network "forwarded_port", guest: 80, host: 8082
      nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Instalar Nginx y configurar balanceo de carga
      nginx.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y nginx
        sudo rm /etc/nginx/sites-enabled/default
        cat <<EOT | sudo tee /etc/nginx/conf.d/load_balancer.conf
        upstream apache_servers {
            server 192.168.56.11;
            server 192.168.56.12;
        }
  
        server {
            listen 80;
            location / {
                proxy_pass http://apache_servers;
            }
        }
  EOT
        sudo systemctl restart nginx
      SHELL
    end
  end