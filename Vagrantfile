# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Box / OS
VAGRANT_BOX = 'ubuntu/bionic64'
# Memorable name for your
VM_NAME = 'vagrant-artifactory'
# VM User 'vagrant' by default
VM_USER = 'vagrant'
# Host folder to sync
HOST_PATH = '.'
# Where to sync to on Guest � 'vagrant' is the default user name
GUEST_PATH = '/home/' + VM_USER + '/' + VM_NAME

$install_script = <<SCRIPT
echo "Installing dependencies ..."
sudo apt-get update

echo Installing Docker...
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker vagrant

echo "Installing docker-compose ..."
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

echo "Create Docker volumes"
sudo docker volume create --name=artifactory_data
sudo docker volume create --name=postgres_data

echo "Download Docker Compose archive"
cd vagrant-artifactory
sudo curl -L "https://releases.jfrog.io/artifactory/bintray-artifactory/org/artifactory/oss/docker/jfrog-artifactory-oss/7.27.6/jfrog-artifactory-oss-7.27.6-compose.tar.gz" -o jfrog-artifactory-oss-7.27.6-compose.tar.gz
tar -xvf  jfrog-artifactory-oss-7.27.6-compose.tar.gz

echo "Copy the docker-compose-volumes.yaml to the extracted folder."
cd artifactory-oss-7.27.6
cp templates/docker-compose-volumes.yaml docker-compose.yaml

echo "Add the entries in the .env file"
echo -e "\n" >> .env
echo -e "JF_SHARED_NODE_IP=$(hostname -i)" >> .env
echo -e "JF_SHARED_NODE_ID=$(hostname -s)" >> .env
echo -e "JF_SHARED_NODE_NAME=$(hostname -s)" >> .env

echo "Manage Artifactory using native Docker Compose commands"
sudo docker-compose -p rt up -d

SCRIPT

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  
  # Section 1: Operating System
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = VAGRANT_BOX
  # Actual machine name
  config.vm.hostname = VM_NAME

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Section 2: Network Settings
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
  config.vm.network "private_network", ip: "192.168.56.30", :name => 'VirtualBox Host-Only Ethernet Adapter'

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Section 3: Folder Settings
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder HOST_PATH, GUEST_PATH

  # Section 4: Provider Settings
  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |v|
    v.name = VM_NAME
    v.memory = 5120
    v.cpus = 2
  end
  
  # Section 5: Provision Settings
  # View the documentation for the provider you are using for more
  # information on available options.
  #config.vm.provision "shell", path: "install-docker.sh"
  config.vm.provision "shell",inline: $install_script, privileged: true

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
