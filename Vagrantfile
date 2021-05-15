# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = "sample"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.disksize.size = '30GB'
  config.mutagen.orchestrate = true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.cpus = 2
    vb.memory = "8192"
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
    vb.customize ["modifyvm", :id, "--usb", "off"]
    vb.customize ["modifyvm", :id, "--usbehci", "off"]
    vb.customize ["modifyvm", :id, "--usbxhci", "off"]
    vb.customize ["modifyvm", :id, "--audio", "none"]
  end

  config.vm.synced_folder './', '/home/vagrant/app', type:"virtualbox",
    rsync_auto: true,
    rsync__exclude: ['.git/', '.envrc']
  config.vm.provision :docker, run: 'always'
  config.vm.provision :docker_compose

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
  SHELL
end
