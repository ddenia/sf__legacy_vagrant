# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
  echo "-------------------- updating package lists"
  apt-get update
  # gotta put this before the upgrade, b/c it reboots and then all commands are lost
  echo "-------------------- installing postgres"
  apt-get install postgresql -y --force-yes
  # fix permissions
  echo "-------------------- fixing listen_addresses on postgresql.conf"
  sudo sed -i "s/#listen_address.*/listen_addresses '*'/" /etc/postgresql/9.3/main/postgresql.conf
  echo "-------------------- fixing postgres pg_hba.conf file"
  # replace the ipv4 host line with the above line
  echo 'local   all         postgres                          peer' > /etc/postgresql/9.3/main/pg_hba.conf
  echo 'local   all         all                               md5'  >> /etc/postgresql/9.3/main/pg_hba.conf
  echo 'host    all         all         0.0.0.0/0             md5'  >> /etc/postgresql/9.3/main/pg_hba.conf

  echo "-------------------- restart postgresql cluster"
  sudo service postgresql restart

  echo "-------------------- creating postgres vagrant role with password vagrant"
  # Create Role and login
  sudo -u postgres psql -c "CREATE USER vagrant WITH SUPERUSER CREATEDB ENCRYPTED PASSWORD 'vagrant'"
  echo "-------------------- creating vagrant database"
  # Create vagrant database
  sudo su postgres -c "createdb -E UTF8 -T template0 --locale=en_US.utf8 -O vagrant vagrant"
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "ubuntu/trusty64"

  # speed up apt-get
  #config.cache.auto_detect = true

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "http://files.vagrantup.com/trusty64.box"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 5432, host: 5433

  # Run the shell script inline provisioner
  config.vm.provision "shell", inline: $script

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network :private_network, ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network :public_network

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

end
