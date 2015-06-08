---
layout: post
title:  "Docker Compose: Run a multi-container app in one command"
date:   2015-05-30 20:30
categories: DockerCompose Multi-app 
banner_image: compose.jpg
comments: true
author_name: Steffi
---

Hi there! In our previous [post](http://learning-continuous-deployment.github.io/docker/images/dockerfile/database/persistence/volumes/linking/container/2015/05/29/docker-and-databases/) you have seen how to containerize a web application by dividing it into three different containers. An application like this that consists of many containers can be launched by just one single command with Docker Compose which will be introduced in this post.  

<!--more-->

##Docker Compose

If your application consists of several Dockerfiles and therefore several containers you can build them individually with the Docker commands. This can get annoying if you have dozens of containers. The solution is Docker Compose. Compose allows you to define a single file for a multi-container application, so in the end only a single command is needed to get everything running. We will show you how this will work in general, but be aware that Compose is not recommended for using it in production yet.

Compose comes with all the necessary [CLI commands](https://docs.docker.com/compose/cli/) for managing your application's life cycle, like e.g. start, stop and rebuild services or view the status of running services. To see the environment variables check out [this link](https://docs.docker.com/compose/env/) but keep in mind that it is not recommended to use them for connecting to linked services. You should rather use the link name. 

##Installation

First you need to install [Compose](https://docs.docker.com/compose/install/). On Windows install Compose with curl and run the commands:

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


##docker-compose.yml 

In this file you specify your app's services. Each service must specify exactly one of `image` or `build`, whereas `image` can be the a remote or local tag or the partial ID and `build` specifies a path or directory containing a Dockerfile. If the path is a relative path it will be relative to the docker-compose.yml itself. 

You do not need to specify options such as `CMD` or `VOLUMES` in docker-compose.yml`because `docker run` respects these options from the Dockerfile. Further options are e.g. to link containers, expose ports or mount paths as volumes. For further reference check out the official [Docker website](https://docs.docker.com/compose/yml/). 


##Sample project

Assuming you have a Python project as in our [first sample app](https://github.com/learning-continuous-deployment/django_project), you have defined the Dockerfile and the `requirements.txt`. Your project is not only a simple webserver but contains also a database (postgres). To specify these services, link everything together and describe which Docker images your `docker-compose-yml` should look something similar like this: 

    db:
      image: postgres
    web:
      build: .
      command: python manage.py runserver 0.0.0.0:8000
      volumes:
        - .:/code
      ports:
        - "8000:8000"
      links:
        - db
    
    
To build the project use `docker-compose run`. The command 

    $ docker-compose run web django-admin.py startproject myproject .

will build an image for the web service with the given Dockerfile. The second part will run inside a container built using this image. The next step is to set up the correct database connection. Therefore you need to change the `DATABASES=...` definition in `myproject/settings.py`, which are defined by the [postgres Docker image](https://registry.hub.docker.com/_/postgres/). 

Finally you run the command `docker-compose up` which will link everything together and that's it!
