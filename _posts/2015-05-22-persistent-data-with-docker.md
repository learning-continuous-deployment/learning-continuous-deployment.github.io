---
layout: post
title: "Persistent Data Storage with Docker"
date: 2015-05-22 11:59:00
categories: docker container volumes
banner_image: data_docker.jpg
comments: true
author_name: Jan
---
In this post we are going to have a look at working with data storage in and outside of containers. After reading you will have a basic understanding of Docker volumes and you will know how to achieve persistent data storage. 
<!--more-->

###Introduction 
You probably read this post, because you think that Docker's concept of packaging small containers running a single process for only a limited amount of time, is quite contradictory with persistent data. At the first glimpse this true, but if you get the idea of using [Volumes](http://crosbymichael.com/advanced-docker-volumes.html), then you will realize, that there is an elegant solution to the persistence problem, which regards the single responsibility principle.

###Volumes
In the Docker ecosystem a volume can be a directory __outside__ the root file system of the container. You can add a data volume by specifying the `-v` option in the Docker `docker run` command. Volumes are intended to provide persistence for your data, which is completely independent of the container's lifecycle. Therefore Docker volumes will not be deleted when you remove the container. Furthermore they won't get garbage collected. 
Under the hood the `-v`option creates a volume in `/var/lib/docker/volumes` (on the host's file system) where all the data will be stored.

There are two distinct use cases for the work with volumes.  

 1. Mount a volume from your Docker daemon's host system (so you can basically *import* a directory from there)
 2. Adding a volume to your container, so that it can be imported in another one

### Mount a volume from the host's file system
So let's have a look at the first use case:
We mount a directory (or file) from the host to the container by setting the `-v` flag to the path on the host system. The path after the `:` specifies where the data will be mounted inside the container. This is important, if you don't put the `:` then a new volume will be created, which is empty. So typing 

    docker run -it /host/dir_a:/home/dir_b ubuntu /bin/bash

will give you a shell to a Ubuntu based container, where the host directory `/host/dir_a` will be mounted in the container at `/home/dir_b`.

Sometimes this can be pretty handy, __but__ mounting a host directory is of course host-dependent and the storage location is not under Docker's control. Another important aspect is, that volumes are container specific, so when working with volumes we loose the portability and the ability to share Docker images. If there are some chunks of data which should persist between two containers, someone __must__ map them manually. Therefore we need another approach. 

###Adding a volume to your container
As mentioned above, we can create a new volume in the container which can then be imported into another container. Let's start simple and create a new volume: 

    docker run -v /data ubuntu


This will create a new volume inside the container at `/data`. Be aware that you can mount multiple volumes. You can achieve the same result by using the `VOLUME` keyword in the Dockerfile. Such volumes on it's own can't solve the *persistence problem*, but lead to the idea of [data-only containers](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/). 

##Data Volume Containers
The most elegant way to share data between containers is the concept of the so-called *data volume containers* which are simple containers running on a barebone image and doing basically nothing, but exposing a volume. This approach perfectly regards the single responsibility principle and is said to work best for production. 
So it is a super simple data-only container:     


    docker create -v /datastore --name datacontainer ubuntu


To access the volume from another container, we use the `--volume-from` flag and pass the container's ID or name. A simple example could look like this:

    docker run -it --volumes-from datacontainer ubuntu /bin/bash
    root@efffe6d65a2f: cd /datacontainer/; touch hello_persistent_world

This will write a file to the `/datastore`-volume of `datacontainer`. To double check that, attach the data-only container to a third one:

    docker run -it --volumes-from datacontainer ubuntu /bin/bash
    root@5850de88cb17: cd /datastore/; ls
    hello_persistent_world

Another fact you should have noticed is that the data-only containers don't need to be running, they just have to exist. To remove the volume and delete the data, you have to explicitly call `docker rm -v` on the last container, which references the volume. 

###References:
* http://www.offermann.us/2013/12/tiny-docker-pieces-loosely-joined.html 
* http://crosbymichael.com/advanced-docker-volumes.html 
* http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/
* http://docs.docker.com/userguide/dockervolumes/ 
* http://container-solutions.com/2014/12/understanding-volumes-docker/ 
* https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e 
