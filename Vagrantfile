Vagrant.configure("2") do |config|
  # Servidor Apache 1
  config.vm.define "web1" do |web1|
    web1.vm.box = "ubuntu/bionic64"
    web1.vm.network "private_network", ip: "192.168.33.10"
    web1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    web1.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y apache2
      sudo ufw allow in "Apache"
      echo '<h1>Web Server 1</h1>' | sudo tee /var/www/html/index.html
    SHELL
  end

  # Servidor Apache 2
  config.vm.define "web2" do |web2|
    web2.vm.box = "ubuntu/bionic64"
    web2.vm.network "private_network", ip: "192.168.33.11"
    web2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    web2.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y apache2
      sudo ufw allow in "Apache"
      echo '<h1>Web Server 2</h1>' | sudo tee /var/www/html/index.html
    SHELL
  end

  # Servidor Nginx (Balanceador de Carga)
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "ubuntu/bionic64"
    nginx.vm.network "private_network", ip: "192.168.33.12"
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    nginx.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y nginx
      sudo ufw allow 'Nginx Full'

      # Configuración de Nginx para el balanceo de carga
      echo 'upstream backend {
        server 192.168.33.10;
        server 192.168.33.11;
      }
      server {
        listen 80;
        location / {
          proxy_pass http://backend;
        }
      }' | sudo tee /etc/nginx/sites-available/load_balancer

      # Habilitar la configuración y reiniciar Nginx
      sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
      sudo rm /etc/nginx/sites-enabled/default
      sudo systemctl restart nginx
    SHELL
  end
end
