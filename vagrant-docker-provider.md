# Using the Vagrant Docker provisioner

Vagrant has the ability to use a variety of providers to aid in the development and deployment of stacks and applications. 
As of Vagrant 2.x there is the ability to use Docker as a provider as an alternative to Virtualbox or Parallels for setting up local environments.

**Table of Contents**

- [Introduction](#introduction)
- [Features](#features)
    - [Batteries Included](#batteries-included)
    - [Nice UI](#nice-ui)
- [Introduction](#introduction)    

### Create a Vagrant project

The first will be to add Vagrant support to the project. This can be done with `vagrant init` but we will start with docker as the default provider.

```
$ vagrant init --provider docker
```

This should generate a Vagrantfile in the new directory. When using the docker provisioner, the `config.vm.box` settings should not be configured. 

This means you can skip the step of download a basebox to create the VM and use Docker to handle the initial setup which gives more flexibility.

```ruby
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  #config.vm.box = "boxcutter/fedora21"

  config.vm.provider "docker" do |d|
    d.build_dir = "."
  end
 
end
```
In a later post we will see how the Docker provider and Ansible provisioner can be used together.

By using the `--provider docker` option the `Vagrantfile` generator will automatically add the `config.vm.provider` block.


#### Docker containers without a service

If you are not starting a running service like mysql, nginx or memceched, the `remains_running = false` setting will need to be false. Otherwise Vagrant will return an error because the container started either never left the "stopped" state or very quickly reverted to the "stopped" state.

```ruby
config.vm.provider "docker" do |d|
    d.build_dir = "."
    d.remains_running = false
end
```

### Create a Dockerfile

To keep things lite we are going to create an instance of Nginx so that we can build the web app. Alpine linux has a packages repo where
you can find the packages you need. 

```
FROM fedora:21
MAINTAINER http://fedoraproject.org/wiki/Cloud

RUN yum -y update && yum clean all
RUN yum -y install nginx && yum clean all
RUN echo "daemon off;" >> /etc/nginx/nginx.conf
RUN echo "nginx on Fedora" > /usr/share/nginx/html/index.html

EXPOSE 80

CMD [ "/usr/sbin/nginx" ]
```

This is using `apk` the package manger in Alpine to download and install nginx. The `EXPOSE` will allow for port `80` to be accessible within the container.

It is important to note, that unlike other providers where the network will need to defined within the Vagrantfile, the ports from the virtual machine will be accessilble to the host (verify?)

In linux this will run the docker instance that is running on the host machine. After starting Vagrant, you can list the docker containers currently on the host machine.

```
$ sudo docker ps -a
CONTAINER ID        IMAGE                                      COMMAND             CREATED             STATUS                           PORTS               NAMES
```

### Start the Vagrant instance

```
$ vagrant up
```

This will start the Vagrant virtual machine, if you are on linux and docker is not in the root group then use `sudo` to start Vagrant.

Once the VM is up, the running container will be visible from within the host machine. On Mac and Windows, if you ssh into the VM, the boot2docker
instance will be available and list the running container. http://stackoverflow.com/a/30398541

```
$ sudo docker ps -a
CONTAINER ID        IMAGE                                      COMMAND             CREATED             STATUS                           PORTS               NAMES
534200e8213e        docker.io/ansible/centos7-ansible:latest   "/bin/bash"         10 minutes ago      Exited (-1) About a minute ago    
```

### Verifying the docker instance

Test the nginx instance is working by trying to `curl` the server running in the Docker container.

```
$ curl -I localhost:80
HTTP/1.1 200 OK
Date: Sun, 26 Jul 2015 15:31:46 GMT
Expires: -1
Cache-Control: private, max-age=0
Content-Type: text/html; charset=ISO-8859-1
Server: gws
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
```

This also creates some additional files within the project directory. 

```
$ tree -a
.
├── Dockerfile
├── .vagrant
│   └── machines
│       └── default
│           └── docker
│               ├── creator_uid
│               ├── docker_build_image
│               ├── id
│               └── index_uuid
└── Vagrantfile
```

There is now a `.vagrant` directory that keeps track of all the Virtual machines for the current project within the `machines` directory.

At the moment there is only one provider that is `default` and that is the Docker configuration from before.

- **creator_uid**: 
- **docker_build_image**: 
- **id**: 
- **index_uuid**: 

### Managing the Vagrant Virtual Machine

When you are done working, you can suspend the VM and this will gracefully stop of the docker containers and shut down the virtual machine. 

```
$ vagrant suspend
```

Once you need to return to the project, navigate to the root and run `vagrant up` to restart the VM instance you had running before.

```
$ vagrant up
```

### Synced folders

Share an additional folder to the guest VM. The first argument is the path on the host to the actual folder. The second argument isthe path on the guest to mount the folder. And the optional third argument is a set of non-required options.

```
config.vm.synced_folder "../data", "/vagrant_data"
```

