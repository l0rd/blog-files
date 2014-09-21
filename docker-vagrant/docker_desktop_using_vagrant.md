## Running a graphical application within a Docker container using Vagrant

This is based on the [docker desktop project](https://github.com/rogaha/docker-desktop)

	$ git clone https://github.com/rogaha/docker-desktop.git
	
This is the Vagrantfile

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

Run the Docker container 
    
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
