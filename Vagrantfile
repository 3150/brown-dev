# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = "brown-dev"
  config.vm.network "private_network", ip: "192.168.80.10"
  config.disksize.size = '100GB'
  config.mutagen.orchestrate = true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.cpus = 4
    vb.memory = "10240"
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
    vb.customize ["modifyvm", :id, "--usb", "off"]
    vb.customize ["modifyvm", :id, "--usbehci", "off"]
    vb.customize ["modifyvm", :id, "--usbxhci", "off"]
    vb.customize ["modifyvm", :id, "--audio", "none"]
  end

  config.vm.synced_folder '../', '/home/vagrant/workdir', type:"virtualbox",
    rsync_auto: true,
    rsync__exclude: ['.git/']
  config.vm.provision :docker

  config.vm.provision "shell",
    privileged: false,
    run: "always",
    inline: <<-SHELL
      sudo apt update -y
      sudo apt install curl docker-compose \
                            net-tools \
                            golang-go \
                            git \
                            autoconf \
                            bison \
                            build-essential \
                            libssl-dev \
                            libyaml-dev \
                            libreadline6-dev \
                            zlib1g-dev \
                            libncurses5-dev \
                            libffi-dev \
                            libgdbm6 \
                            libgdbm-dev \
                            libdb-dev -y
      sudo timedatectl set-timezone 'Asia/Singapore'

      if [ ! -e ~/.goenv ]; then
        git clone https://github.com/syndbg/goenv.git ~/.goenv
        grep -q 'export GOENV_ROOT="$HOME/.goenv"' ~/.profile || echo 'export GOENV_ROOT="$HOME/.goenv"' >> ~/.profile
        grep -q 'export PATH="$GOENV_ROOT/bin:$PATH"' ~/.profile || echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.profile
        grep -q 'eval "$(goenv init -)"' ~/.profile || echo 'eval "$(goenv init -)"' >> ~/.profile
        source ~/.profile
      fi

      if [ ! -e ~/.rbenv ]; then
        git clone https://github.com/rbenv/rbenv.git ~/.rbenv
        grep -q 'export PATH="~/.rbenv/bin:$PATH"' ~/.profile || echo 'export PATH="~/.rbenv/bin:$PATH"' >> ~/.profile
        grep -q 'eval "$(rbenv init -)"' ~/.profile || echo 'eval "$(rbenv init -)"' >> ~/.profile
        source ~/.profile
        if [ ! -e "$(rbenv root)"/plugins/ruby-build ]; then
          git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
        fi
      fi

      if [ ! -e ~/.nvm ]; then
        curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
        grep -q 'export NVM_DIR="$HOME/.nvm"' ~/.profile || echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.profile
        grep -q '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' ~/.profile || echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.profile
        grep -q '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' ~/.profile || echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' >> ~/.profile
        source ~/.profile
        nvm install node
      fi

      goenv versions | grep '1.16.2' || goenv install 1.16.2
      goenv versions | grep '1.16.3' || goenv install 1.16.3
      goenv versions | grep '1.16.4' || goenv install 1.16.4
      goenv versions | grep '1.16.5' || goenv install 1.16.5

      rbenv versions | grep '2.6.7' || rbenv install 2.6.7
      rbenv versions | grep '2.7.3' || rbenv install 2.7.3
      rbenv versions | grep '3.0.1' || rbenv install 3.0.1

      nvm install v15.14.0
      nvm install v16.1.0
      nvm install v16.2.0
      nvm install v16.3.0
    SHELL
end
