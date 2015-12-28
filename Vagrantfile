# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'fileutils'
require 'pathname'

VAGRANT_COMMAND = ARGV[0]

VAGRANTFILE_API_VERSION = "2" if not defined? VAGRANTFILE_API_VERSION

VM_RAM_SIZE = 1024
VM_CPU_CORE = 1
VG_BOX_NAME = "CentOS7"
SWARM_MASTER = 1

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  #ensure VirtablBox provider as 1st serve.
  config.vm.provider "virtualbox"
  config.vm.provider "parallels"
  config.vm.provider "vmware_fusion"

  config.vm.box = "#{VG_BOX_NAME}"
  if Vagrant.has_plugin?("vagrant-cachier")
    	# Configure cached packages to be shared between instances of the same base box.
    	# More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
    		config.cache.scope = :box
  end
  config.ssh.insert_key = false
  config.hostmanager.enabled = true
  config.vm.hostname = 'docker-host'
  config.vm.define "docker-host"
  config.vm.network :private_network, :ip => '192.168.201.101'

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "#{VM_RAM_SIZE}"]
    vb.customize ["modifyvm", :id, "--cpus", "#{VM_CPU_CORE}"]
  end

  config.omnibus.chef_version = :latest

  config.vm.provision "chef_solo" do |chef|
    chef.add_recipe "firewall"
    chef.add_recipe "htop"
    chef.add_recipe "btrfs"
    chef.add_recipe "docker"
    chef.add_recipe "docker::compose"

    chef.json = {
      "docker" => {
        'group_members' => ['vagrant'],
        'options' => '--dns 8.8.8.8 --dns 8.8.4.4 --host=tcp://0.0.0.0:2375 --host=unix:///var/run/docker.sock'
      }
    }
  end

end
