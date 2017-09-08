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

  # ALL CONFIG
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false

  # VM DB CONFIG
  config.vm.define "db" do |db|
    db.vm.network "forwarded_port", guest: 5432, host: 5432
    db.vm.network "private_network", ip: "192.168.1.11"
    db.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "dj-database"
      vb.memory = "512"
    end
    db.vm.provision  "shell", inline: <<-SHELL
      sudo apt-get install -y postgresql
      sudo su - postgres -c "psql -f /vagrant/db-config.sql"
      sudo su - postgres -c 'echo "host all all 192.168.1.10/24 trust" >> /etc/postgresql/9.3/main/pg_hba.conf'
      sudo su - postgres -c "echo listen_addresses=\\'*\\' >> /etc/postgresql/9.3/main/postgresql.conf"
      sudo service postgresql restart
    SHELL
  end

  # VM WEB CONFIG
  config.vm.define "web" do |web|
    web.vm.network "forwarded_port", guest: 8000, host: 8000
    web.vm.network "private_network", ip: "192.168.1.10"
    web.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "dj"
      vb.memory = "512"
    end
    web.vm.provision  "shell", inline: <<-SHELL
      sudo apt-get install -y python-pip python-dev libpq-dev postgresql postgresql-contrib
      sudo pip install django flake8 psycopg2
      cd /vagrant/
      chmod +x manage.py
      python manage.py migrate
      python manage.py loaddata db.fixture.json
    SHELL
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.


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
  #    #config.vm.network :private_network, ip: settings["ip"] ||= "192.168.10.10"
  #   config.vm.network "public_network", :bridge => "en0: Wi-Fi (Airport)", :ip => "192.168.1.200"
  # config.vm.provider "virtualbox" do |vb|
  #  # Display the VirtualBox GUI when booting the machine
  #  vb.gui = false
  #  vb.name = "dj-database"
  #  vb.cpus = 1
  #  # Customize the amount of memory on the VM:
  #  vb.memory = "512"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.

end
