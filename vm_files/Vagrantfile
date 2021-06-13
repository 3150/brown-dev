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

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update -y
    sudo apt install docker-compose net-tools -y
    sudo timedatectl set-timezone 'Asia/Singapore'
  SHELL
end
