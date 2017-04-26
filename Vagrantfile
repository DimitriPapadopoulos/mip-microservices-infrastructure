# -*- mode: ruby -*-
# vi: set ft=ruby :

# Demo of Airflow in a Vagrant box.
#
# You need to install the vagrant-hostmanager plugin before running vagrant up to start the VM:
#
#   vagrant plugin install vagrant-hostmanager
#   vagrant up
#

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "ubuntu/xenial64"

  if Vagrant.has_plugin? 'vagrant-hostmanager'
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.aliases = []
  else
    fail_with_message "vagrant-hostmanager missing, please install the plugin with this command:\nvagrant plugin install vagrant-hostmanager"
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  config.vm.define "demo" do |demo|

    demo.vm.hostname = "demo"

    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine.
    demo.vm.network :forwarded_port, guest: 4080, host: 14080 # Airflow
    #demo.vm.network :forwarded_port, guest: 5050, host: 15050 # mesos - not working due to restricted ip listen
    demo.vm.network :forwarded_port, guest: 5080, host: 15080 # marathon

    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    demo.vm.network :private_network, ip: '192.168.34.66'

    # Shared folders
    demo.vm.synced_folder "../../../roles", "/etc/ansible/roles", create: true
    demo.vm.synced_folder "data/out", "/data/SHARE", create: true

  end

    # Provider-specific configuration so you can fine-tune various
    # backing providers for Vagrant. These expose provider-specific options.
    # Example for VirtualBox:
    #
    config.vm.provider "virtualbox" do |vb|
      vb.name = "mip-local-ubuntu"

      # Use VBoxManage to customize the VM. For example to change memory:
      vb.customize ["modifyvm", :id, "--memory", "5072", "--cpus", "3", "--ioapic", "on"]
      vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
    end
    #
    # View the documentation for the provider you're using for more
    # information on available options.

  ansible_inventory_dir = "envs/vagrant/etc/ansible"

  config.vm.provision "shell", inline: "grep demo.local /etc/hosts || echo '192.168.34.66 demo demo.local' >> /etc/hosts"
  # Mapping ansible roles with their git repositories messes with etckeeper
  config.vm.provision "shell", inline: "mkdir -p /etc/ansible && [ -f /etc/ansible/.gitignore ] || echo 'roles/' > /etc/ansible/.gitignore"
  # Xenial ships only with Python 3
  config.vm.provision "shell", inline: "sudo apt-get update && sudo apt-get install -y python2.7 python-simplejson"

end