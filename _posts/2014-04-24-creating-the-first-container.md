---
layout: post
title:  "Creating the first Container on a Server"
date:   2015-04-24 16:00:00
categories: jenkins container dockerfile
banner_image: docker-jenkins-git.png
comments: true
author_name: Steffi, Marius
---

This post will explain how you can create a Jenkins job that will create a Docker Container which is configured by its dockerfile. Later on it will be deployed on another remote server, so our application will run in an own container. 

#Our Project and the Dockerfile
As our project is written in Django the Dockerfile needs to trigger a Python download with all the related dependencies. This will be done in the Dockerfile by adding

    FROM django:python2-onbuild 
    RUN pip install --upgrade pip
to the Dockerfile. Other dependencies can be found in the file *requirements.txt*, so we are able to use Python dependencies such as *numpy* or *matplotlib*. 

#Setup Jenkins 
* Create a new Jenkins job as described in one of our previous posts ["How to trigger a Jenkins build process by a GitHub push"](http://learning-continuous-deployment.github.io/jenkins/github/2015/04/17/github-jenkins/).
* With installing the Docker plugin on Jenkins you will have more options for your build process. As we want to trigger a build with a new push from GitHub we tick the box "Build when a change is pushed to GitHub". But you could also predefine a time schedule. Furthermore the Docker plugin allows to build when a new image is built on DockerHub.
* If you want to test whether your container is working at all, you could try starting it through a shell first with `docker build -f "$WORKSPACE/Dockerfile" -t "csm_$BUILD_ID" $WORKSPACE`. Commands with a `$`in front are environment variables from Jenkins. `f`indicates the file name which can be found in our project workspace. `t` stands for the repository name for the image. The Docker commands can be found [here](https://docs.docker.com/reference/commandline/cli/).
* Instead using the shell we would like to use the provided Docker plugin functions such as "Execute Docker Container", which will be described here *soon*.  

##Troubleshooting
1. Avoid spaces in your Jenkins project name.
2. If you want to change the language in Jenkins simply change it in your browser. For Firefox use e.g. the add-on [Quick Locale Switcher](https://addons.mozilla.org/en-US/firefox/addon/quick-locale-switcher/). 
3. If you want to start a docker container through a shell script, you need to be an administrator, however `sudo` will not work. So it is required to create a docker group if it does not already exist.
 * `sudo groupadd docker`
 * Add the connected user `${USER}` to the docker group. `sudo gpasswd -a ${USER} docker`
 * Restart the Docker daemon with `sudo service docker restart` (for Ubuntu 14.04 use `sudo service docker.io restart`)
 * Restart your Jenkins server. 