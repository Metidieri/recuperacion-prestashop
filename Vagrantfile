# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.


config.vm.define "balancer" do |balancer|
	balancer.vm.box = "generic/debian11"
	balancer.vm.hostname = "PedroMetiBalanceador"	
	balancer.vm.network "public_network"
	balancer.vm.network "private_network", ip: "172.16.1.10",
				virtualbox__intnet: "red1"
	balancer.vm.provision "shell", path: "scriptbalancer.sh"
end

config.vm.define "nginx1" do |nginx1|
	nginx1.vm.box = "generic/debian11"
	nginx1.vm.hostname = "PedroMetiNginx1"
	nginx1.vm.network "private_network", ip: "172.16.1.20",
				virtualbox__intnet: "red1"
	nginx1.vm.network "private_network", ip: "192.168.1.20",
				virtualbox__intnet: "red2"
	nginx1.vm.provision "shell", path: "scriptnginx.sh"
end

config.vm.define "nginx2" do |nginx2|
	nginx2.vm.box = "generic/debian11"
	nginx2.vm.hostname = "PedroMetiNginx2"	
	nginx2.vm.network "private_network", ip: "172.16.1.30",
				virtualbox__intnet: "red1"
	nginx2.vm.network "private_network", ip: "192.168.1.30",
				virtualbox__intnet: "red2"
	nginx2.vm.provision "shell", path: "scriptnginx.sh"
end

config.vm.define "nfs" do |nfs|
	nfs.vm.box = "generic/debian11"
	nfs.vm.hostname = "PedroMetiNFS"	
	nfs.vm.network "private_network", ip: "172.16.1.40",
				virtualbox__intnet: "red1"
	nfs.vm.provision "shell", path: "scriptnfs.sh"
end

config.vm.define "mysql" do |mysql|
	mysql.vm.box = "generic/debian11"
	mysql.vm.hostname = "PedroMetiMySQL"	
	mysql.vm.network "private_network", ip: "192.168.1.10",
				virtualbox__intnet: "red2"
	mysql.vm.provision "shell", path: "scriptmysql.sh"
end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

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

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
