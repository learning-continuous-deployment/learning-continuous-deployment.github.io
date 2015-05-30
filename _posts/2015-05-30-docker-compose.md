---
layout: post
title:  "Get an app running in one command: Docker Compose"
date:   2015-05-30 20:30
categories: docker, docker compose
banner_image: compose.jpg
comments: true
author_name: Steffi
---

Hi there! In our previous [post](http://learning-continuous-deployment.github.io/docker/images/dockerfile/database/persistence/volumes/linking/container/2015/05/29/docker-and-databases/) you have seen how to containerize a web application by dividing it into three different containers. Our next goal will be to start the application from one single Dockerfile using Docker Compose which will be introduced in this post.  

<!--more-->

##Introduction

So far our [sample application](https://github.com/learning-continuous-deployment/java-mongodb-sample) consists of the data-only container, the DB-Container that runs mongod and the application container that runs the business logic. To run our application we defined several Dockerfiles and build the images with the help of Jenkins in two jobs. Could that be achieved easier? Yes, with using Compose. Compose allows you to define a single file for a multi-container application, so in the end only a single command is needed to get everything running. We will show you how this will work for our sample application, but be aware that Compose is not recommended for using it in production yet.

Compose comes with all the necessary commands for managing your application's lifecycle, like e.g. start, stop and rebuild services or view the status of running services. To see the environment variables check out [this link](https://docs.docker.com/compose/env/) but keep in mind that it is not recommended to use them for connecting to linked services. You should rather use the link name. 

##Installation

First you need to install [Compose](https://docs.docker.com/compose/install/). To install Compose with curl, run the commands:

     curl -L https://github.com/docker/compose/releases/download/1.2.0/docker-compose-`u name -s`-`uname -m` > /usr/local/bin/docker-compose
     chmod +x /usr/local/bin/docker-compose

or as Python package:

    $ sudo pip install -U docker-compose

Verify your installation with `docker-compose --version`.


##Three steps

Basically there are just three steps to use Compose and run your entire app with only one command. 

 1. Define the Dockerfile for your app that defines its whole environment.
 2. Define the services that make up your app, so they can be run in an isolated environment. This will be done in `docker-compose.yml`.
 3. Run `docker-compose up`. 

That sounds quite simple, so let's give it a try. We will provide you with more information about the usage of Compose in our sample project asap.

...
![Post under construction]({{site.url}}/assets/images/construction.jpg)