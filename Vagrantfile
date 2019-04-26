#-*- mode: ruby -*-
# vi: set ft=ruby ai si et ts=2 sw=2 sts=2:
#
# Vagrant configuration to setup build enviroment for asuswrt-merlin project.

# Tweak .bashrc of 'vagrant' user to put the host volume into $PATH
SCRIPT=<<-SHELL
sed -i 's!^# for examples!PATH="/vagrant:$PATH"!' "$HOME/.bashrc"
SHELL


Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/xenial32"
  config.vm.boot_timeout = 300
  config.vm.box_check_update = false
  config.vm.provision "shell", inline: SCRIPT, privileged: false

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

end
