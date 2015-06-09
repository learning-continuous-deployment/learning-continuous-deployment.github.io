---
layout: post
title:  "Getting started..."
date:   2015-04-21 16:43:00
categories: docker jenkins git
banner_image: jenkins-docker.jpg
comments: true
author_name: Steffi
---


Hi there! Continue reading if you are unfamiliar with Docker or Jenkins. To get started we provide you with some general information about both tools regarding their installation and the continuous deployment process.

 <!--more-->

## Docker

In general Docker provides a container based solution for virtualisation. A container isolates a process so it seems to run on a standalone OS, although it still runs on the same OS as its host and therefore shares the same kernel. You could use it to develop, ship and run applications. Furthermore you can integrate Docker into a continuous deployment workflow. It also helps to set up a testing environment so you can run multiple apps on several operating systems - on the same server. 

Is Docker then the same as a VM? No, because a Docker image is basically just a filesystem, so you can only run one command in the container, whereas a VM image can have many running services. 

The backend consists of a Linux-Container (LXC) that is responsible for managing the tasks. A LXC itself is a collection of functions that are offered by the Linux-Kernel due to the purpose of Sandboxing. Ordinarily a Docker Container is a full file system of a Linux installation. The focus is on the application itself rather than on the container or even the container's operating system. 
This virtualisation technique is based on the usage of Cgroups and Namespacing, both coming from LXC. Cgroups (Control Groups) are Kernel functions to define process groups to restrict resources (such as RAM or Disc I/O) by a specified group. Namespacing focuses the security as single processes or Cgroups can be hidden from others.

### Dockerfiles

Thus your application can be deployed as a Docker Container. Docker files are responsible for the configuration of these containers and allow you to create an automated build system. They are written in a Domain Specific Language (DSL) and contain instructions to set up a Docker image. It is a text document that contains the responsible commands to build a Docker image and should be created at the root of your repository. The command `docker build` is run by the Docker daemon (not the CLI) and needs root privileges (how to create dockergroups will be explained in another [post](http://learning-continuous-deployment.github.io/jenkins/container/dockerfile/2015/04/24/creating-the-first-container/)). 

### DockerHub

[DockerHub](https://hub.docker.com) is the public repository of all Docker images that can be pulled. Any user can push images as well but needs to be registered on Docker. 


###Installation

There is a well documented [installation guide](https://docs.docker.com/installation/#installation) on the Docker website itself. To get to know the first commands you could also go through their [tutorial](https://www.docker.com/tryit/#).

For future reference Docker will be installed on our server *continuousdeployment-2*. On both our servers (Docker and Jenkins) run Ubuntu 14.04 with the Kernel Version 3.13.

### Possible Errors 

Errors that occurred in our setup: 

* Make sure the environment variable of GitHub's ssh.exe is set correctly otherwise add it to your path `set PATH=%PATH%;C:\Program Files (x86)\Git\bin` 
* Installing Docker on a VM with Ubuntu 14.04 requires a Kernel Version > 3.10 


## Jenkins and Git

Another tool in our continuous deployment process will be Jenkins, originally called Hudson. We will use a Jenkins integration server as a trigger. A push in our repository will trigger the Jenkins server so that we are able to get a Docker environment. This can be achieved by the [Docker Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Docker+Plugin) for Jenkins. 
To be able to trigger a Jenkins build with a Git push you need to install a [Git Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin) as well. 

Our general continuous deployment workflow will look like the following:
![Continuous Deployment Workflow]({{site.url}}/assets/images/deployment_workflow.jpg)

In general Jenkins monitors the execution of predefined jobs, such as testing or building software projects. Therefore Jenkins is a popular tool for continuous integration especially considering the execution with automated tests. It provides a continuous integration system to make it easier to integrate changes for a fresh build. Due to build automation the productivity will be increased. There are many free plugins to use and furthermore it is quite flexible and easy to adapt to your own purposes. 

###Installation

You will need a JRE 1.6 or later. As we are using a Debian-based distribution, we could [install Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu) through apt-get by simply putting these lines in the terminal on our server:

    wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add - 
    sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
    sudo apt-get update
    sudo apt-get install jenkins

For future reference Jenkins will be installed on our server *continuousdeployment-1*. 
