# Docker meets the IDE

This blog post is about integrating Docker's magic into our IDEs. This will give us the opportunity to introduce a plugin to edit, build and run Docker containers inside Eclipse: [doclipser](http://www.github.com/domeide/)

## Docker in development environments

Before going into the details of Docker and IDEs integration, let's argument why we think docker has to be in our list of essentials development tools.

### Build system
First of all, Docker allow us to make our *build* *environments* portable, repeatable and isolated. Let's make an example: you need to build a C project using gcc v5.1. All you need to do is run the following command (assuming, of course, that you have docker installed):
```sh
docker run gcc:5.1 gcc -o helloworld helloword.c
```
The magic is that you can run it anywhere, you don't have to bother about libraries, conflicts or installing gcc. If it runs on your laptop it will run on the integration server too.

### Runtime environment
The same apply to *runtime* *environments*. If you need to run your java web application within tomcat 8 you can just use:
```sh
docker run tomcat:8 -v HelloWorld.war:/usr/local/tomcat/webapps/Helloworld.war 
```
Again you don't have to worry about dependencies, platforms configurations or linux distributions differences. It just works.

## Docker and IDEs

Ok, it should be clear why Docker is useful for development now. And we have illustrated that with a bunch of commands you can use on your favorite shell. And we love our shells. The problem is that a trait of remarcable developers is [inspired laziness](http://blog.codinghorror.com/get-me-the-laziest-people-money-can-buy/). This mean that if we are coding inside our favorite IDE we don't want to get outside of it. We want to run containers right from the IDE.

Unfortunately Docker isn't supported by major IDEs right now.

So how would you like to see Docker integrated in your IDE?

*Would you run your IDE inside a container?*

```sh
docker run eclispse
```
Naaaaa....That not the way containers works. It's still too tricky and not portable to run graphical applications inside containers.

*Would you run containers from within your IDE?*

Oh Yeahhh! And with that we would love to see Dockerfiles support, Compose yml files support, IDE build systems and running environment running inside containers. Let's see the details:

### Dockerfile support

Dockerfile support should come with syntax highilighting, autocomplete (dockerfile instructions popping out at your CTRL+SPACE) and syntax validation (syntax errors should be shown by your IDE before you `docker build`)

![syntaxh](/syntaxh.png)\ ![autocomplete](/autocomplete.png)\ ![syntax verification](/syntaxvalid.png)

### Compose yml file support

Even more interesting would be Compose yml file support. That would allow to define inter container relation as links and volumes and run multiple containers with one click from your editor. That is cool!

![compose](/compose.png)

### IDE Build system

Of course you would need support for running containers from the IDE. Specifically containers that can build your source files. That could be even more easy if leveraging Docker language stacks.

![buildsystems](/buildsystems.png)


### IDE Runtime Environments

And the last feature we want in our IDE is the possibility to run runtime enviroments inside Docker containers. Right from your IDE.

![runenv](/runenv.png)


## Introducing Doclipser

With these features in mind we built [doclipser](http://www.github.com/domeide/). An eclipse plugin to edit, build and run docker containers.

Doclipser has Dockerfile support: syntax highlighting, autocomplete and syntax verification. It still doesn't have compose yml files support but support a few Docker commands that allow you to build source files or launch runtime environments.

![doclipser](https://github.com/domeide/doclipser/raw/master/images/doclipserdemo.gif)


## domeide.github.io

Doclipser isn't the only tool that brings Docker inside your IDE. We are building a github page to collect all of these plugins: [domeide.github.io](http://domeide.github.io). Here are some of these :

* [Sublime Docker](https://packagecontrol.io/packages/Docker%20Based%20Build%20Systems)
* [IntelliJ IDEA 14.1](http://blog.jetbrains.com/idea/2015/03/docker-support-in-intellij-idea-14-1/)
* [Eclipse JBoss Tools](http://tools.jboss.org/blog/2015-03-30-Eclipse_Docker_Tooling.html)
* [Visual Studio 2015 RC Tools for Docker - Preview extension](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Do you have any tool do you want to share ?
