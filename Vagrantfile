# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "debian/bullseye64"
  
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.linked_clone = true
  end

  # Común a todas las máquinas
  
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update -y
    apt-get upgrade -y
  SHELL

  ####################################################################
  # dns.sistema.sol
  ####################################################################
  
  config.vm.define "dns" do |dns|

    dns.vm.provider "virtualbox" do |vb|
      vb.name = "http-lab-1-dns"
      vb.memory = "256"
    end

    dns.vm.hostname = "tierra.sistema.sol"    
    dns.vm.network :private_network, ip: "192.168.57.103", hostname: true

    dns.vm.provision "bind9", type: "shell", inline: <<-SHELL
      apt-get install -y bind9 bind9-utils bind9-doc

      # c
      cp -v /vagrant/dns/named /etc/default/
      sudo cp -v /vagrant/dns/resolv.conf /etc
      cp -v /vagrant/dns/named.conf.options /etc/bind
      cp -v /vagrant/dns/named.conf.local /etc/bind
      cp -v /vagrant/dns/sistema.sol.dns /var/lib/bind
      cp -v /vagrant/dns/192.168.57.dns /var/lib/bind

      systemctl restart bind9
      systemctl status bind9
    SHELL
    
  end

  ####################################################################
  # mercurio.sistema.sol
  ####################################################################

  config.vm.define "mercurio" do |mercurio|
    mercurio.vm.provider "virtualbox" do |vb|
      vb.name = "http-lab-1-web"
      vb.memory = "256"
    end

    mercurio.vm.hostname = "mercurio.sistema.sol"
    mercurio.vm.network :private_network, ip: "192.168.57.101", hostname: true
    
    mercurio.vm.provision "apache", type: "shell", inline: <<-SHELL
      # Instalar apache2
      sudo apt install apache2
      sudo cp -v /vagrant/dns/resolv.conf /etc

      # Copiar ficheros de configuración de apache2 que incluyen
      # apache2.conf, apolo.sistema.sol.conf y soyuz.sistema.sol
      # a su lugar correcto
      sudo mkdir /var/www/apolo.sistema.sol /var/www/soyuz.sistema.sol
      sudo cp -v /vagrant/web/apache2.conf /etc/apache2/apache2.conf
      sudo cp -v /vagrant/web/apolo.sistema.sol.conf /etc/apache2/sites-available/apolo.sistema.sol.conf
      sudo cp -v /vagrant/web/soyuz.sistema.sol.conf /etc/apache2/sites-available/soyuz.sistema.sol.conf
      sudo cp -vR /vagrant/web/soyuz.sistema.sol /var/www/
      sudo cp -vR /vagrant/web/apolo.sistema.sol /var/www/
      # Enlazamos el directorio raíz de nuestras webs dentro de el
      # servidor con la carpeta que tenemos fuera de la MV

#      if ! [ -L /var/www/apolo.sistema.sol ]; then
#       sudo rm -rf /var/www/apolo.sistema.sol 
#       sudo ln -fs /vagrant/web/apolo.sistema.sol /var/www/apolo.sistema.sol
#     fi
#
#     if ! [ -L /var/www/soyuz.sistema.sol ]; then
#       sudo rm -rf /var/www/soyuz.sistema.sol
#       sudo ln -fs /vagrant/web/soyuz.sistema.sol /var/www/soyuz.sistema.sol
#     fi
# 
      # Habilitar los servidores virtuales

        sudo a2ensite apolo.sistema.sol.conf
        sudo a2ensite soyuz.sistema.sol.conf

      # Reiniciar el servicio  
        systemctl restart apache2
    SHELL
  end
end
