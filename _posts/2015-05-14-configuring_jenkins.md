---
layout: post
title:  "Jenkins shell commands [Update]"
date:   2015-05-24 17:18:00
categories: docker jenkins
banner_image: configuring_jenkins.jpg
comments: true
author_name: Markus
excerpt_separator: <!--more-->
---

This post provides a brief overview of the possibilities we have configuring Jenkins in order to automatically trigger essential build phases that are needed to set up an environment for Docker Containers.
<!--more-->

##Overview
In our second post ["How to trigger a Jenkins build process by a GitHub push"](http://learning-continuous-deployment.github.io/jenkins/github/2015/04/17/github-jenkins/) we showed how to configure Jenkins to get notice of new GitHub pushes that are coming in when we add new features or bugfixes to our project.
Now it's time to configure Jenkins so that it reacts on those changes and triggers shell commands to build a new image and send it to our second server (as described in this [post](http://learning-continuous-deployment.github.io/docker/images/dockerfile/2015/04/24/exporting-docker-container/)) which will finally run the container and expose it to the public.
It would be possible to install a Docker plugin for Jenkins to achieve these tasks as well - they'd provide you with some user interface elements especially for Docker interactions. You can find them [here](https://wiki.jenkins-ci.org/dosearchsite.action?queryString=docker). But for our use case we will just stick with the shell commands.

##Triggering shell commands
In your Jenkins project settings add a `new build step` and choose `run shell`.
Now you have a window to write shell commands.

Our shell commands look like this at the moment, we will go through the commands shortly:

![Shell commands]({{site.url}}/assets/images/shell_commands.png)

##Explaining the commands
The first two commands build a new docker image and save it to a .tar file to prepare it for sending to the other server.

      docker build -f "$WORKSPACE/Dockerfile" -t "csm_$BUILD_ID" $WORKSPACE
      docker save -o django-app-image.tar "csm_$BUILD_ID"

Then we transfer this tarball to the other server via scp

      scp django-app-image.tar root@continuousdeployment-2.mi.hdm-stuttgart.de:/home/docker

Next, we ssh to the second server - this will provide us with a bash to execute commands directly on the second server.
We have to `load` the image from the tarball, then we run a little script to stop all currently running containers and finally run the newly loaded container and expose it to the public.

      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "/root/stopContainer.sh"
      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "/root/deleteAllImages.sh" || true
      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "docker load < /home/docker/django-app-image.tar"
      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "docker run -d -p 80:1234 -name csm_$BUILD_ID csm_$BUILD_ID python3 manage.py runserver 0.0.0.0:1234"

You may have noticed that we use scripts to stop containers and remove images, strangely it did not work directly via ssh commands. We will come back to this on a later stage why this happened.
The scripts just contain a single standard command.

**[UPDATE 24.05.2015]**
The problem with commmands that contain stacked calls like `$(...)` is actually that these commands will be executed on the current machine - which in our case is wrong because we ssh to our second server.
Another solution would be to include ssh after the `$`, but this would clutter the commands too much and make them error prone against typos.

`stopContainer`: With the `--filter=""` tag we can filter our containers by name. This is the name we previously assigned to the image in the run command with `-name`. This way we can make sure not to stop images from our other projects for example.

      docker stop $(docker ps -a --filter="name=csm" -q)

`deleteAllImages`: This command is implemented to save disk space on the server by deleting unused images and containers - one of our images uses about 2.4 GB! The shell command is followed by `|| true` because we do not want any console output to fail our Jenkins build. For example if there are no images to clean, this command will return a message we so not want to receive at this point. We can still see those messages in the Jenkins output console tough.

      docker rm $(docker ps --no-trunc -aq)
      docker rmi $(docker images -q --filter "dangling=true")
