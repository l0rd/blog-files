# Setting up a development environment using docker and vagrant

![docker+vagrant](/Users/mariolet/Development/blog-files/vagrant-docker/images/docker+vagrant.png)

Often considered two alternative tools, Docker and Vagrant can be used together to build repeatable and portable development environments.

### What's wrong with development environments

* __How long does it takes to setup your development environment?__
 
   Setting up a development environment should be a relatively easy task to complete. Open your favourite shell, checkout a file from the repository and execute a command. This should be sufficient to build your project and be ready for development.
   
   But that's theory. In practice setting up the development environment of legacy project includes some tasks that are difficult to automate and maintain with a simple shell script. A non comprehensive list includes :
   
  * Customising configuration files
  * Installing OS patches
  * Using hardcoded paths when 
  * Resolving certificates issues
  * Installing old versions of tools and frameworks to avoid conflicts

* __How close is your development environment to your CI environment?__

    Have you ever skipped some tests because they failed on your machine? Or even worst, have you ever break the official build because tests ran successfully on your machine but failed consistently on CI server?

    Any slight difference your environment has in respect to the CI environment or other team members environments can result in an unexpected behaviour of build and test processes. Diverging can be as simple as giving a try to the last version of a framework or switching temporary on different project that has different dependencies. 
    
    Finding out what makes your system behave differently is an annoying task every developer want to avoid.
    
### Virtual environments and Docker

As a consequence development environment should have two characteristics:
 
__Isolated__: you don't want to mess it when testing some new tools or when setting up development environments for other projects.   
__Repeatable__: the same environment should be consistently reproduced on every team member machine and on CI and production servers.

These can be easily achieved using virtual environments. But classic VMs are resource consuming and not fit to be stored in version control systems. Developers, that need to code/build/test every few minutes (or even seconds), accepted grudgingly the overhead introduced by virtual environments. However VMs have been popular amongst operators because these shortcoming where not considered critical.  

[Docker](https://www.docker.com/) has solved these problems and has virtual environments has become extremely popular in developers community too:

> In its first 12 months Docker usage rapidly spread among startups and early adopters who valued the platform’s ability to separate the concerns of application development management from those of infrastructure provisioning, configuration, and operations.  Docker gave these early users a new, faster way to build distributed apps as well as a “write once, run anywhere” choice of deployment from laptops to bare metal to VMs to private and public clouds.

Docker containers are extremely fast to start up and consume dramatically less resources. Moreover Docker images can be versioned on a VCS and it is possible to [do a diff](https://docs.docker.com/reference/commandline/cli/#diff) between the current container status and it's image as you could do between 2 versions of the same source file.

### Using Docker to configure a repeatable development environment

As an example we are going to setup an environment to build and test a [Vert.x](http://vertx.io/) application.

To install Docker you can refer to the [official doc](https://docs.docker.com/installation/) or use [get docker script](https://get.docker.io/).

Once Docker installed we have to write a Dockerfile to specify the content of our container. Here is the one we are going to use to setup the Vert.x development environment :

    FROM ubuntu:14.04

    # Install dev tools: jdk, git etc...
    RUN apt-get update
    RUN apt-get install -y openjdk-7-jdk maven git wget vim

    # jdk7 is the default jdk
    RUN ln -fs /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java /etc/alternatives/java

    # Install vertx
    RUN \
      mkdir -p /usr/local/vertx && cd /usr/local/vertx && \
      wget http://dl.bintray.com/vertx/downloads/vert.x-2.1.2.tar.gz -qO - | tar -xz

    # Add vertx to the path
    ENV PATH /usr/local/vertx/vert.x-2.1.2/bin:$PATH

    RUN mkdir -p /usr/local/src
    WORKDIR /usr/local/src

    CMD ["bash"]

`FROM ubuntu:14.04` defines our docker container base image. To make sure we did a good choice we are using the same base image the [docker team uses](https://github.com/docker/docker/blob/master/Dockerfile) :-). You can find a comprehensive list of Docker base images at the [docker hub](https://registry.hub.docker.com/).

Subsequent commands are modifications that will be applied on top of the base image:

* Installing development tools using apt-get: openjdk, maven, git, wget, vim
* Downloading and installing vertx
* Adding vertx bin folder to the path
* Creating folder /local/src and making it the default working directory

Dockerfiles are really straightforward. The reference can be find [here](https://docs.docker.com/reference/builder/).

We can now build the Docker image running the following command from folder Dockerfile is:
	
	$ sudo docker build -t=vertxdev .

We just build an image, named vertxdev, that has git installed. We can use it to fetch the source code from vertx github repository.

	$ sudo docker run -t --rm -v /src/vertx/:/usr/local/src vertxdev git clone https://github.com/vert-x/vertx-examples.git
	
Note that the source code will be cloned inside the container, in its working dir: /usr/local/src. To make the code persist, even after the container has been stopped and removed, we need to bind mount it from the host '-v /src/vertx/:/usr/local/src'. The '-t' flag is to get git command output and '--rm' to remove the container as soon as it exits.

Now that the source code has been fetch we can build and run one of the vertx examples. Beware that the command 'vertx run' does both (build and execution) at once.
    
    $ sudo docker run -d -v /src/vertx/:/usr/local/src -p 8080:8080 vertxdev vertx run vertx-examples/src/raw/java/httphelloworld/HelloWorldServer.java | xargs sudo docker logs -f
    
And you should see the following message
   
    Succeeded in deploying verticle
	
You can now press CTR+C to terminate docker logs -f command (vertx verticle will continue running in background though).

To test the application you use curl, wget or any browser :

	$ curl localhost:8080
	Hello World


### Using vagrant to make docker containers portable

Docker (actually [libcontainer](https://github.com/docker/libcontainer) which is a Docker module) still requires Linux kernel 3.8 or higher and x86_64 architecture. This bounds considerably the environments Docker can natively run on. 

Vagrant v1.6, which has been released in May, supports [docker-based development environment](http://www.vagrantup.com/blog/vagrant-1-6.html#features). Using Vagrant, Docker can run on Mac and Windows too. That's not the only tool that allow to run Docker on non linux platform: boot2docker is and is the official one  can do it too but it has at least 3 shortcomings:

* Setting up Mac and Windows (boot2docker) is different from setting up Linux (docker)
* Boot2Docker is tied to a specific linux distro ([tiny core linux](http://distro.ibiblio.org/tinycorelinux/)) whereas Vagrant can target any linux distribution that satisfies docker requirements
* Vagrant can help orchestrating containers: linking and folder sharing can be done using a configuration file

To overcome this limitation a Linux VM that satisfies these requirements allow to run Docker almost on every platform (Mac OS and windows included). [boot2docker](), a specially crafted VM using extremely fast linux distro ([tiny core linux](http://distro.ibiblio.org/tinycorelinux/)) as guest OS has rapidly become the standard and is now officially supported by the docker team. The problem with this approach is that if the Docker containers to run on different  the setup 

Vagrant is an open-source software that provides a method for creating repeatable development environments across a range of operating systems. Vagrant use virtual machines from various providers (virtualbox, VMware, etc..). First released in 2010 Vagrant supports Docker as a provider since a few months.  

> On systems that can't run Linux containers natively, such as Mac OS X or Windows, Vagrant automatically spins up a ” host VM" to run Docker. This allows your Docker-based Vagrant environments to remain portable, without inconsistencies depending on the platform they are running on.

Let’s setup the previous vertx example using vagrant
Install [Vagrant]() and [Virtualbox]()

Create the following Vagrantfile and copy it where the Dockerfile is :

    ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

    Vagrant.configure("2") do |config|
      config.vm.define "vertxdev" do |a|
        a.vm.provider "docker" do |d|
          d.build_dir = "."
          d.build_args = ["-t=vertxdev"]
          d.ports = ["8080:8080"]
          d.name = "vertxdev"
          d.remains_running = true
          d.cmd = ["vertx", "run", "vertx-examples/src/raw/java/httphelloworld/HelloWorldServer.java"]
          d.volumes = ["/src/vertx/:/usr/local/src"]
        end
      end
    end

We will clone vertx-examples repository as done with docker but using vagrant instead:

    $ vagrant docker-run vertxdev -- git clone https://github.com/vert-x/vertx-examples.git

This commands is used to run one-off commands against a Docker container. When the command is executed the container is removed. Note that under the hoods vagrant builds the image and executes the container.

We are ready to run the container as specified in the Vagrantfile 
    
    $ vagrant up

To get the log from the docker container 
    
    $ vagrant docker-logs
    
    ==> vertxdev: Succeeded in deploying verticle
    
If you want to test it you need to ssh into the docker host. To do so you need to get the id of vagrant default docker-host:

    $ vagrant global-status
    id       name     provider   state   directory                                                         
    -------------------------------------------------------------------------------------------------------
    c62a174  default  virtualbox running /Users/mariolet/.vagrant.d/data/docker-host
    
Using the id you can easily issue the curl command to test if the verticle is answering correctly
     
    $ vagrant ssh c62a174 -c "curl localhost:8080"
    
    Hello World
    
### Haven't you said identical?

The docker host VM that Vagrant starts on OSes doesn't support Docker natively (e.g. Mac and Windows) is a boot2docker VM. But what if our CI run Ubuntu (thus doesn't need boot2docker at all)? Haven't we said that development environments should be as close as possible to CI, staging and production environments? Happily Vagrant let us specify a Docker host VM other than the default boot2docker.

This can be done adding in the Vagrantfile a couple of settings that specifies the name of the Docker host VM and its Vagrantfile. Indeed the Docker host VM will be described through a Vagrantfile too :

      config.vm.define "vertxdev" do |a|
        a.vm.provider "docker" do |d|
          [...]
          d.vagrant_machine = "dockerhost"
          d.vagrant_vagrantfile = "./DockerHostVagrantfile"
        end
      end

Here is a sample Vagrantfile for a Docker host with Ubuntu Server 14.04 LTS instead of the default boot2docker distro:

    Vagrant.configure("2") do |config|

      config.vm.provision "docker"

      config.vm.provision "shell", inline:
        "ps aux | grep 'sshd:' | awk '{print $2}' | xargs kill"
          
      config.vm.define "dockerhost"
      config.vm.box = "ubuntu/trusty64"
      config.vm.network "forwarded_port",
        guest: 8080, host: 8080

      config.vm.provider :virtualbox do |vb|
          vb.name = "dockerhost"
      end

    end

Note that with configuring a custom docker host has another benefit: we can now specify VM forwarded ports

    config.vm.network "forwarded_port",
            guest: 8080, host: 8080

This means that we are able to accessing the vertx application (that run within a docker container, that run inside the docker host) from the main host OS. Executing from my mac terminal :

 

Of course host VMs are not limited to Ubuntu. More vagrant boxes can be found on [Vagrant Cloud](https://vagrantcloud.com). Interesting boxes are CoresOS, the default boot2docker box and a modified one.

### Orchestration

We are not covering here orchestration between containers running on remote docker hosts. For this kind of Vagrant is definitly not the right tool. . But when your containers are running on the same host Vagrant can defintely help to run multiple containers and let then interact using its so called ["multi-machine" environment](https://docs.vagrantup.com/v2/multi-machine/).

#### Multiple containers

As a first exemple we will leverage vertx [Event Bus Point to Point example](https://github.com/vert-x/vertx-examples/tree/master/src/raw/java/eventbus_pointtopoint). That's really straighforward. We are going to reuse the same Dockerfile and configure two VM boxes: "vertxreceiver" and "vertxsender". The first will run Receiver.java as docker CMD, the latter Sender.java. Here is the Vagrantfile:

    ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'
    DOCKER_HOST_NAME = "dockerhost"
    DOCKER_HOST_VAGRANTFILE = "./DockerHostVagrantfile"

    Vagrant.configure("2") do |config|

      config.vm.define "vertxreceiver" do |a|
        a.vm.provider "docker" do |d|
          d.build_dir = "."
          d.build_args = ["-t=vertxreceiver"]
          d.name = "vertxreceiver"
          d.remains_running = true
          d.cmd = ["vertx", "run", "vertx-examples/src/raw/java/eventbus_pointtopoint/Receiver.java","-cluster"]
          d.volumes = ["/src/vertx/:/usr/local/src"]
          d.vagrant_machine = "#{DOCKER_HOST_NAME}"
          d.vagrant_vagrantfile = "#{DOCKER_HOST_VAGRANTFILE}"
        end
      end

      config.vm.define "vertxsender" do |a|
        a.vm.provider "docker" do |d|
          d.build_dir = "."
          d.build_args = ["-t=vertxsender"]
          d.name = "vertxsender"
          d.remains_running = true
          d.cmd = ["vertx", "run", "vertx-examples/src/raw/java/eventbus_pointtopoint/Sender.java","-cluster"]
          d.volumes = ["/src/vertx/:/usr/local/src"]
          d.vagrant_machine = "#{DOCKER_HOST_NAME}"
          d.vagrant_vagrantfile = "#{DOCKER_HOST_VAGRANTFILE}"
        end
      end

    end

To start the two containers just execute vagrant up. To have access to containers stdoug vagrant docker-logs 

    $ vagrant up
    ...
    $ vagrant docker-logs
    ==> vertxsender: Starting clustering... 
	==> vertxsender: No cluster-host specified so using address 172.17.0.18 
	==> vertxsender: Succeeded in deploying verticle
	==> vertxreceiver: Starting clustering... 
    ==> vertxreceiver: No cluster-host specified so using address 172.17.0.19 
    ==> vertxreceiver: Succeeded in deploying verticle 
    ==> vertxreceiver: Received message: ping!
    ==> vertxsender: Received reply: pong
    ==> vertxreceiver: Received message: ping!
    ==> vertxreceiver: Received message: ping!
    ==> vertxsender: Received reply: pong
    ==> vertxsender: Received reply: pong
    ...

#### Linking containers

    ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

    Vagrant.configure("2") do |config|

      config.vm.define "vertxdev" do |a|
        a.vm.provider "docker" do |d|
          d.image = "vertxdev:latest"
          d.ports = ["8080:8080"]
          d.name = "vertxdev"
          d.remains_running = true
          d.cmd = ["vertx", "run", "vertx-examples/src/raw/java/httphelloworld/HelloWorldServer.java"]
          d.volumes = ["/src/vertx/:/usr/local/src"]
          d.vagrant_machine = "dockerhost"
          d.vagrant_vagrantfile = "./DockerHostVagrantfile"
        end
      end

      config.vm.define "vertxdev-client" do |a|
        a.vm.provider "docker" do |d|
          d.image = "vertxdev:latest"
          d.name = "vertxdev-client"
          d.link("vertxdev:vertxdev")
          d.remains_running = false
          d.cmd = ["wget","-qO", "-","--save-headers","http://vertxdev:8080"]
          d.vagrant_machine = "dockerhost"
          d.vagrant_vagrantfile = "./DockerHostVagrantfile"
        end
      end

    end


    $ vagrant up --no-parallel
    
    $ vagrant docker-logs
    ==> vertxdev: Succeeded in deploying verticle 
    ==> vertxdev-client: HTTP/1.1 200 OK
    ==> vertxdev-client: Content-Type: text/plain
    ==> vertxdev-client: Content-Length: 11
    ==> vertxdev-client: 
    ==> vertxdev-client: Hello Worl
    
### Dude where is my IDE?

Docker container aren't made to run GUI. Although you could use a VNC client to connect to a docker container and run an application that need a graphical interface, installing an IDE in a docker container doesn't make sense. That is one of the few cases where Docker is not recommended. 

IDE such as Eclipse or IntelliJ should be installed outside Docker containers. Source code should be shared between the host and docker containers using a docker volume. Code can be fetch outside  

#### Running the IDE on the host

    ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

    Vagrant.configure("2") do |config|

      config.vm.synced_folder "src", "/usr/local/src"
  
      config.vm.define "vertxdev-src" do |a|
        a.vm.provider "docker" do |d|
          d.image = "l0rd/vertxdev:latest"
          d.ports = ["8080:8080"]
          d.name = "vertxdev-src"
          d.remains_running = true
          d.cmd = ["vertx", "run", "vertx-examples/src/raw/java/httphelloworld/HelloWorldServer.java"]
        end
      end

    end
    
    
#### Running a graphical IDE within a Docker container

	$ git clone https://github.com/rogaha/docker-desktop.git
	

    ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

    Vagrant.configure("2") do |config|

      config.vm.define "docker-desktop" do |a|
        a.vm.provider "docker" do |d|
          d.build_dir = "."
          d.build_args = ["-t=l0rd/docker-desktop"]
          has_ssh = true
          d.name = "docker-desktop"
          d.remains_running = true
        end
      end

    end
    
    $ vagrant up

Have a look at the stdout to get docker password

    $ vagrant docker-logs  | grep Password # <== Get the password

Issue ssh-config command to get the docker container IP address
    
    $ DOCKER_IP=$(vagrant ssh-config | grep HostName | awk '{print $2}')
    $ DOCKER_HOST=$(vagrant global-status | grep default | awk '{print $1}')

Use SSH tunnel to forward host port 9000 to docker-desktop container port 22 

    $ vagrant ssh $DOCKER_HOST -- -L 9000:$DOCKER_IP:22    
    
Starting a new session
 
    $ ssh docker@localhost -p 9000 "sh -c './docker-desktop -s 800x600 -d 10 > /dev/null 2>&1 &'"

Attaching to the session started
    
    $ xpra --ssh="ssh -p 9000" attach ssh:docker@localhost:10 


### Conclusions

Fig
Other Docker usages
And if Bill Gates too talks about containers 