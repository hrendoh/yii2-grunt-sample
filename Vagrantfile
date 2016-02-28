# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 9000, host: 9000

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./", "/home/vagrant/workspace", group: "www-data", mount_options: ["dmode=775,fmode=664"]

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password root'
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password root'
    sudo apt-get update
    sudo apt-get install -y apache2 php5 php5-cli php5-mysql libapache2-mod-php5 php5-mcrypt mysql-client mysql-server
    mysql -uroot -pmypassword <<< 'create database yii2basic'
    sudo a2enmod expires
    sudo a2enmod rewrite
    VHOST=$(cat <<EOF
<VirtualHost *:80>
    DocumentRoot "/home/vagrant/workspace/web"
    <Directory "/home/vagrant/workspace/web">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
EOF
)
    sudo echo "${VHOST}" > /etc/apache2/sites-available/000-default.conf
    sudo service apache2 restart
    curl -Ss https://getcomposer.org/installer | php
    sudo mv composer.phar /usr/bin/composer
    sudo composer global require "fxp/composer-asset-plugin:~1.1.1"
    curl -sL https://deb.nodesource.com/setup_5.x | sudo -E bash -
    sudo apt-get install -y nodejs
    sudo npm install -g grunt-cli
    cd workspace && composer install && npm install
  SHELL
end
