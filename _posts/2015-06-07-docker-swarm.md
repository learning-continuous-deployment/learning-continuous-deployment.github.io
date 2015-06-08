---
layout: post
title:  "Docker Swarm for Clustering"
date:   2015-06-07 15:00
categories: DockerSwarm
banner_image: swarm2.jpg
comments: true
author_name: Steffi
---

In a distributed environment things can get quite complicated when you have to deal with more than one host for Docker containers. Docker Swarm helps to cluster your application so it pools several Docker Engines and exposes them as a single virtual Docker engine. In this post we want to present how Docker Swarm works.

<!--more-->

##Setup

[Docker Swarm](https://docs.docker.com/swarm/) allows you to manage a resource pool of Docker hosts and schedule containers automatically managing workload. But keep in mind that it is as well as [Docker Compose](http://learning-continuous-deployment.github.io/docker,/docker/compose/2015/05/30/docker-compose/) still in beta. Do not use it in production yet, things are still likely to change! 

As a prerequisite you need to install Docker 1.4.0 or later on *all* nodes. For a successful communication between the Swarm manager and its node agent (on each node), each node must listen to the network interface (tcp port). The setup is quite easy since Swarm ships as a standard Docker image without any external infrastructure dependencies. Install Swarm with 

    docker pull swarm 

which will use the [official Docker image](https://registry.hub.docker.com/_/swarm/). The general setup contains three basic steps: 	

 1. Run a command to create a cluster. 
 2. Run a command to start Swarm. 
 3. Run a command to join the cluster on each host where the Docker Engine is running. 
  
Each swarm node will run a swarm node agent. You can create a swarm cluster by typing: 

    $ docker run --rm swarm create
   
This will return a unique cluster id, that you need to start the Swarm agent on a node. 

The next steps require a login into each node. Firstly you need to ensure that the docker remote API is available over TCP for the Swarm Manager, so you start the docker daemon:
    
	$ docker -H tcp://0.0.0.0:2375 -d

Secondly you need to register the Swarm agents. Replace `<node_ip>` and `<cluster_id>` with your correct settings: 
    
	docker run -d swarm join --addr=<node_ip:2375> token://<cluster_id>
	
And as a last step you start the Swarm manager on any machine with: 
    
	docker run -d -p <swarm_port>:2375 swarm manage token://<cluster_id>
  
To check your configuration you can run 

    docker -H tcp://<manager_ip:manager_port> info 
	
You can use the regular docker CLI to access your nodes. Use [this link](https://docs.docker.com/swarm/API/) to check out the Docker Swarm API. To list all your nodes, use: 

    docker run --rm swarm list token://<cluster_id>
    
For example if you want to schedule a Redis container requiring 1 GB of memory, use:

    $ docker run -d -P -m 1g redis
    
By using constraints you can meet the specific requirements of each container. To run MySQL on a host with flash storage, simply put:

    docker run -d -e constraint:storage==ssd mysql 




