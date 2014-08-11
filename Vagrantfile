# -*- mode: ruby -*-
# vi: set ft=ruby :

project_name = "bamx"
ip_address = "33.33.33.10"

Vagrant.configure("2") do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  config.vm.hostname = "bamx"

  # Every Vagrant virtual environment requires a box to build off of.
  #config.vm.box = "ubuntu/precise64"
  config.vm.box = "virtualbox"

  # Uses vagrant-omnibus plugin to install chef on machine
  #config.omnibus.chef_version = "10.14.2"
  config.omnibus.chef_version = "11.4.4"
  #config.omnibus.chef_version = "latest"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  #config.vm.box_url = "https://dl.dropboxusercontent.com/u/165709740/boxes/quantal64-vanilla.box"
  config.vm.box_url = "https://vagrantcloud.com/ubuntu/trusty64/version/1/provider/virtualbox.box"

  # Assign this VM to a host-only network IP, allowing you to access it
  # via the IP. Host-only networks can talk to the host machine as well as
  # any other machines on the same network, but cannot be accessed (through this
  # network interface) by any external networks.
  config.vm.network :private_network, ip: "33.33.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.

  # config.vm.network :public_network

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 3306, host: 33066
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 443, host: 4403

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder ".", "/vagrant", mount_options: ['dmode=777','fmode=666']

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider :virtualbox do |vb|
  #   # Don't boot with headless mode
  #   vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # The path to the Berksfile to use with Vagrant Berkshelf
#   config.berkshelf.berksfile_path = "./Berksfile"

  # Enabling the Berkshelf plugin. To enable this globally, add this configuration
  # option to your ~/.vagrant.d/Vagrantfile file
#   config.berkshelf.enabled = true

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to exclusively install and copy to Vagrant's shelf.
  # config.berkshelf.only = []

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.except = []

#   config.vm.provision :shell do |sh|
#     sh.inline = <<-EOF
#       sudo apt-get update
#       sudo apt-get install ruby --yes
#       sudo gem install chef --version 11.4.4 --no-ri --no-rdoc
#     EOF
#   end
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.vm.define project_name do |node|
    node.vm.hostname = project_name + ".local"
    node.vm.network :private_network, ip: ip_address
    node.hostmanager.aliases = [ "www." + project_name + ".local" ]
  end

  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "cookbooks"

    # List of recipes to run
    chef.add_recipe "apt"
    chef.add_recipe "build-essential"
    chef.add_recipe "mysql::server"
    chef.add_recipe "redisio::install_from_package"
    chef.add_recipe "redisio::client"
    chef.add_recipe "redisio::default"

    chef.json = {
      :mysql => {
        :bind_address => '0.0.0.0',
        :allow_remote_root => true,
        :server_root_password => '',
        :server_debian_password => '',
        :server_repl_password => ''
      }
    }

  end

  config.vm.provision :shell do |sh|
    #sh.privileged = true
    sh.inline = <<-EOF
      mysql -e 'create database if not exists overholt;'
    EOF
  end
end
