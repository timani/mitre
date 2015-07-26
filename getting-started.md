
### Install vagrant

Check to see if Vagrant is confirmed

```
$ vagrant --version
Vagrant 1.7.4
```

Create a project directory

```
$ mkdir mitre
$ cd mitre/
```

Check the currently installed baseboxes

```
$ vagrant box list
laravel/homestead (virtualbox, 0.1.6)
```

Add the boxcutter fedora 21 image

```
$ vagrant box add boxcutter/fedora21
==> box: Loading metadata for box 'boxcutter/fedora21'
    box: URL: https://atlas.hashicorp.com/boxcutter/fedora21
This box can work with multiple providers! The providers that it
can work with are listed below. Please review the list and choose
the provider you will be working with.

1) parallels
2) virtualbox

Enter your choice: 2
==> box: Adding box 'boxcutter/fedora21' (v2.0.1) for provider: virtualbox
    box: Downloading: https://atlas.hashicorp.com/boxcutter/boxes/fedora21/versions/2.0.1/providers/virtualbox.box
==> box: Successfully added box 'boxcutter/fedora21' (v2.0.1) for 'virtualbox'!
```

Add the centos 6 + docker box-cutter basebox

```
$ vagrant box add box-cutter/centos66;
==> box: Loading metadata for box 'box-cutter/centos66'
    box: URL: https://atlas.hashicorp.com/box-cutter/centos66
This box can work with multiple providers! The providers that it
can work with are listed below. Please review the list and choose
the provider you will be working with.

1) parallels
2) virtualbox
3) vmware_desktop

Enter your choice: 2
==> box: Adding box 'box-cutter/centos66' (v2.0.0) for provider: virtualbox
    box: Downloading: https://atlas.hashicorp.com/box-cutter/boxes/centos66/versions/2.0.0/providers/virtualbox.box
==> box: Successfully added box 'box-cutter/centos66' (v2.0.0) for 'virtualbox'!
```

Create a new project with the basebox

```
$ vagrant init boxcutter/fedora21;
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

A new file will be added to the `mitre` directory

```
$ ll
total 4
-rw-rw-r--. 1 timani timani 3030 Jul 25 23:19 Vagrantfile
```

This will generate the boilerplate for the Vagrantfile

```
$ cat Vagrantfile 
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "boxcutter/fedora21"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
end
```

