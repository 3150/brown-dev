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

      install_go() {
        if [ ! -e $HOME/.goenv ]; then
          echo 'Installing Go'
          git clone https://github.com/syndbg/goenv.git $HOME/.goenv
          echo 'export GOENV_ROOT="$HOME/.goenv"' >> $HOME/.profile
          echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> $HOME/.profile
          echo 'eval "$(goenv init -)"' >> $HOME/.profile
          source $HOME/.profile
        fi

        declare -n versions=$1
        for v in ${versions[@]}; do
          goenv versions | grep $v || goenv install $v
        done

        echo 'Finished installing Go'
      }

      install_ruby() {
        if [ ! -e $HOME/.rbenv ]; then
          echo 'Installing Ruby'
          git clone https://github.com/rbenv/rbenv.git $HOME/.rbenv
          git clone https://github.com/rbenv/ruby-build.git $HOME/.rbenv/plugins/ruby-build
          echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> $HOME/.profile
          echo 'eval "$(rbenv init -)"' >> $HOME/.profile
          source $HOME/.profile
        fi

        declare -n versions=$1
        for v in ${versions[@]}; do
          echo 'rbenv versions'
          rbenv versions | grep $v || rbenv install $v
        done

        echo 'Finished installing Ruby'
      }

      install_node() {
        if [ ! -e $HOME/.nvm ]; then
          echo 'Installing Node'
          curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
          echo 'export NVM_DIR="$HOME/.nvm"' >> $HOME/.profile
          echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> $HOME/.profile
          source $HOME/.profile
        fi

        source $NVM_DIR/nvm.sh

        declare -n versions=$1
        for v in ${versions[@]}; do
          nvm ls | grep $v || nvm install $v
        done

        echo 'Finished installing Node'
      }
  
      install_rust() {
        if [ ! -e $HOME/.cargo ]; then
          echo 'Installing Rust'
          curl https://sh.rustup.rs -sSf | sh -s -- -y
          echo 'export PATH=$HOME/.cargo/bin:$PATH' >> $HOME/.profile
          source $HOME/.profile
        fi
      }

      declare -a GO_VERSIONS=("1.16.2" "1.16.3" "1.16.4" "1.16.5")
      declare -a RUBY_VERSIONS=("2.6.7" "2.7.3" "3.0.1")
      declare -a NODE_VERSIONS=("v15.14.0" "v16.1.0" "v16.2.0" "v16.3.0")

      install_go GO_VERSIONS &
      install_ruby RUBY_VERSIONS &
      install_node NODE_VERSIONS &
      install_rust &
      wait
    SHELL
end
