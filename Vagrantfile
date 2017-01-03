# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  GUEST_RUBY_VERSION = '2.3.3'

  config.vm.box = "vagrant-centos-6.7"
  config.vm.box_check_update = true
  config.vm.box_url = "https://github.com/CommanderK5/packer-centos-template/releases/download/0.6.7/vagrant-centos-6.7.box"

  config.vm.hostname = "centos-7-for-rails"

  config.vm.network "forwarded_port", guest: 3000, host: 3000

  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "shell", privileged: true, inline: <<-SHELL
    function install {
      echo installing $1
      shift
      yum -y install "$@" >/dev/null 2>&1
    }
    yum -y update >/dev/null 2>&1
    install "development tools" kernel kernel-devel kernel-headers dkms gcc gcc-c++ glibc-headers openssl-devel readline libyaml-devel readline-devel zlib zlib-devel
    install "Git" git
    install "sqlite" sqlite sqlite-devel
    yum install -y http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm >/dev/null 2>&1
    install "MySQL" mysql mysql-server mysql-devel
    chkconfig --add mysqld
    chkconfig --level 345 mysqld  on
    echo add repository
    yum install install epel-release
    echo install nodejs
    yum install -y install nodejs npm --enablerepo=epel
  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    echo installing rbenv
    git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
    git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    source ~/.bash_profile
    echo 'gem: --no-ri --no-rdoc' >> ~/.gemrc
    echo installing ruby#{GUEST_RUBY_VERSION}
    rbenv install #{GUEST_RUBY_VERSION}
    rbenv global #{GUEST_RUBY_VERSION}
    echo installing Bundler
    gem install bundler -N >/dev/null 2>&1
    rbenv rehash
    gem install rails -v 4.2.5.2
  SHELL
end
