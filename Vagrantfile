# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "generic/ubuntu2204"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
Vagrant.configure("2") do |config|

  config.vm.define "ubu1" do |ubu1|  
    ubu1.vm.hostname = "ubu1"
    ubu1.vm.box = "generic/ubuntu2204"
    ubu1.vm.network "public_network", ip: "192.168.18.201"
    ubu1.vm.provider :libvirt do |v|
      v.memory = 1024
      v.cpus = 1
    end

    #cambio de contraseña
    ubu1.vm.provision "shell", inline: <<-SHELL
  echo "vagrant:josan1234" | chpasswd
  sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
  systemctl restart ssh
SHELL

# Crear usuario
  ubu1.vm.provision "shell", inline: <<-SHELL
    # Crear el usuario "josan"
    useradd -m -s /bin/bash josan
    # Establecer la contraseña del usuario
    echo "josan:josan1234" | chpasswd
    # Añadir el usuario al grupo sudo
    usermod -aG sudo josan
  SHELL
  end

  config.vm.define "ubu2" do |ubu2|
    ubu2.vm.hostname = "ubu2"
    ubu2.vm.box = "generic/ubuntu2204"
    ubu2.vm.network "public_network", ip: "192.168.18.202"
    ubu2.vm.provider :libvirt do |v|
      v.memory = 1024
      v.cpus = 1
    end

    #cambio de contraseña
ubu2.vm.provision "shell", inline: <<-SHELL
  echo "vagrant:josan1234" | chpasswd
  sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
  systemctl restart ssh
SHELL

# Crear usuario
  ubu2.vm.provision "shell", inline: <<-SHELL
    # Crear el usuario "josan"
    useradd -m -s /bin/bash josan
    # Establecer la contraseña del usuario
    echo "josan:josan1234" | chpasswd
    # Añadir el usuario al grupo sudo
    usermod -aG sudo josan
  SHELL

  end
end

