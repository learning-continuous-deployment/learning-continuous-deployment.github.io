---
layout: post
title:  "Configuring Jenkins"
date:   2015-05-14 17:18:00
categories: docker jenkins
banner_image: configuring_jenkins.png
comments: true
author_name: Markus
excerpt_separator: <!--more-->
---

This blogpost provides a brief overview of the possibilities we have configuring Jenkins in order to automatically trigger essential build phases that are needed to setup an environment for docker containers.
<!--more-->

##Overview
In our second Blogpost ["How to trigger a Jenkins build process by a GitHub push"](http://learning-continuous-deployment.github.io/jenkins/github/2015/04/17/github-jenkins/) we showed off how to configure Jenkins to get notice of new Github pushes that are coming in when we add new features or bugfixes to our project.
Now it's time to configure Jenkins so that it reacts on those changes and triggers shell commands to build a new image and send it to our second server (as described in this [Blogpost](http://learning-continuous-deployment.github.io/docker/images/dockerfile/2015/04/24/exporting-docker-container/)) which will finally run the container and expose it to the public.
It would be possible to install a docker plugin for Jenkins to achieve these tasks as well - they'd provide you with some user interface elements specifically for docker interactions. You can find them [here](https://wiki.jenkins-ci.org/dosearchsite.action?queryString=docker). But for our usecase we'll just stick with the shell commands.

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

      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "docker load < /home/docker/django-app-image.tar"
      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "/root/stopContainer.sh"
      ssh root@continuousdeployment-2.mi.hdm-stuttgart.de "docker run -d -p 80:1234 csm_$BUILD_ID python3 manage.py runserver 0.0.0.0:1234"

You may have noticed that we use a script to stop containers, strangely it did not work directly via ssh commands. We will come back to this on a later stage why this happened.
The script just contains a single standard command:

      docker stop $(docker ps -a -q)
