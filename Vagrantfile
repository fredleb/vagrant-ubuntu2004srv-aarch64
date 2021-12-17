# -*- mode: ruby -*-
# vi: set ft=ruby :

# The base of the the hostname
HOSTNAME_BASE = "node"

# The TLD domain the hosts belong to
DOMAIN_NAME = "private.lan"

# The number of VMs managed by this script
VM_COUNT = 4

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  #config.vm.box = "fredleb/ubuntu2004-server-aarch64"
  config.vm.box = "ubuntu2004-server-aarch64"

  # ARM + Ubuntu on a x86_64 machine is not fast when trying to spwan loads of
  # machines at once. So give it a bit more time than the default 5 min.
  config.vm.boot_timeout = 600 # 10 mins

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Create several machines
  (1..VM_COUNT).each do |i|
    name = "#{HOSTNAME_BASE}#{i}"
    config.vm.define "#{name}" do |server|
      server.vm.hostname = "#{name}.#{DOMAIN_NAME}"

      server.vm.provider :libvirt do |libvirt|
        libvirt.driver = "qemu"
        libvirt.storage_pool_name = "big_pool"
        libvirt.memory = 2048
        libvirt.cpus = 2
        libvirt.machine_type = "virt"
        libvirt.machine_arch = "aarch64"
        libvirt.cpu_mode = "custom"
        libvirt.cpu_model = "cortex-a72"
        libvirt.graphics_type = "none"
        libvirt.features = ['acpi',  'gic version=\'2\'']
        libvirt.loader = "/usr/share/edk2-armvirt/aarch64/QEMU_CODE.fd"
        libvirt.nvram = "/home/vm/nvram/nvram-#{name}.fd"
      end
    end
  end

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
