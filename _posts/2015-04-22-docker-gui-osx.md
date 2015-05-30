---
layout: post
title: "How to get a GUI to a Docker Container on OS X"
date: 2015-04-22 09:59:00
categories: docker images dockerfile
banner_image: docker_on_osx_banner.jpg
comments: true
author_name: Jan
---

This post will explain how to use -e DISPLAY flag on Mac OS X, so that you can get a GUI to your container. Furthermore it covers the basic steps of installing the boot2Docker VM, that runs the Docker engine, on OS X.
<!--more-->   

##How to install Docker on OS X
###Step by step instruction

On OS X there is the convenient possibility to install all necessary tools via Homebrew:
    
    brew install caskroom/cask/cask-brew
    brew install virtualbox
    brew install docker
    brew install boot2docker

Now you're ready to fire up the boot2docker VM with the docker engine:  
    
    boot2docker init
    boot2docker up
    %Display the environment variables for the Docker client:  
    boot2docker shellinit

To test your installation, you can now start your first Docker container by typing:  
```docker run -t -i ubuntu:14.04 /bin/bash```  
This starts an interactive session and gives you a shell in the container.

##How to get a GUI on OS X
On a Linux host you can do pretty basic X11 forwarding. A guide on how to do it, can be found [here](http://fabiorehm.com/blog/2014/09/11/running-gui-apps-with-docker/) or [here](https://registry.hub.docker.com/u/batmat/docker-eclipse/).
On OS X there is no comparable way of achieving this, __but__ there is a crude way to get a GUI for your Docker container. A discussion about the topic and the explanations of the smart people who discovered that, can be found [here](https://github.com/docker/docker/issues/8710).   

You need socat, which is a command line based utility that establishes two bidirectional byte streams and transfers data between them, and XQuartz - Apples version of the X server.  
    
    brew install socat   
    brew cask install xquartz

So start XQuartz:  

    open -a XQuartz

Expose local xquartz socket via socat on a TCP port  

    socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"

Now all you have to do is pass the display to the Container:  

    % in another window   
    docker run -it -e DISPLAY=192.168.59.3:0 batmat/docker-eclipse

![Docker container running Eclipse Luna]({{site.url}}/assets/images/docker_eclipse_osx.png)

Note: the IP saved in `DISPLAY` is the one of your virtual box host. 

