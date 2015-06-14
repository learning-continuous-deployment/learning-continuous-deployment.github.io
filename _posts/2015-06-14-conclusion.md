---
layout: post
title:  "Docker in a nutshell: our conclusion"
date:   2015-06-14 12:00
categories: Docker Conclusion
banner_image: 
comments: true
author_name: Jan, Steffi, Markus, Marius
---

In our blog *Learning Continuous Deployment* we wanted to show you the general basics how to develop *dockerized* apps and the benefits of using Docker. This post summarizes our learnings and gives our final conclusion. 

<!--more--> 

##Containerization in general

Docker offers a container based solution for virtualization. It encapsulates the application and its dependencies into a system and language-independent package, whereas your code runs in isolation from other containers but share the host's resources. Therefore you do not need a VM with an entire guest OS (overhead), but only your container to run your app. 

![Virtual Machines vs. Docker]({{site.url}}/assets/images/vm-docker.png)

##Docker's Technology
Docker is based on Linux Containers (LXC).

![Kernel features]({{site.url}}/assets/images/kernel-features.PNG)

Namespaces help to isolate a workspace, so changes of a global resource are only visible for processes that are part of this Namespace. Cgroups help to distribute the available hardware resources (CPU, I/O, memory) to the processes and manages them. This abstraction layer simplifies the work with containers and its management which leads to portabilty, so you can easy share containers with machines. The reuse of containers is another important principle as well as the their version management.

The Docker Engine is the virtualization technology. Docker uses a client-server architecture in which the user interacts with the Docker daemon (client interface). The daemon is responsible for build proccesses, running containers and their distribution. 

##Docker's use cases
The slogan *build once, run anywhere* indicates many use cases for Docker application. It supports Continuous Integration as well as Continuous Delivery and Continuous Deployment. In our project we used Docker to provide a Continuous Deployment pipeline with Jenkins and GitHub. 

It is possible to use Docker containers for local development so it allows you to run GUI application inside a Docker container, e.g. NetBeans with all necessary dependencies. So you can share the container with other developers without any further setup. This speeds up and simplifies the process. 

##Testing
For our Continuous Deployment pipeline the idea was that Jenkins commands the testing infrastructure to different docker applications - that run simultaneously and run tests in parallel. Why should you not just use Jenkins though? Because Docker provides you a clean environment for every fresh build and increases the speed of test cases (quick and easy to start a container). Because your application is *frozen* within a container it will always run as tested. Furthermore it is easy to switch to another version, e.g. if some dependencies have changed or you want to update or downgrade which will be achieved by simply using another container. Last but not least it is cheaper than running tests on several VM's. 

##Our continuous deployment workflow
Our [first project](http://learning-continuous-deployment.github.io/) showed how to trigger a Jenkins job by a GitHub push that creates images and containers. The new containers will be pushed to our Docker server that runs the container.

##Docker and persistence
Our [second project](http://learning-continuous-deployment.github.io/docker/images/dockerfile/database/persistence/volumes/linking/container/2015/05/29/docker-and-databases/) showed how to achieve persistence in Docker containers by using [Volumes]((http://learning-continuous-deployment.github.io/docker/container/volumes/2015/05/22/persistent-data-with-docker/)). Volumes can be used to mount directories or files from your host machine inside a container. It can be used to create dir which exists as a 
normal* dir. 

##Tools and complex workflows 
There are several tools from Docker to orchestrate your application. [Docker Compose](http://learning-continuous-deployment.github.io/dockercompose/multi-app/2015/05/30/docker-compose/) allows you to run your stack with only one command and describe your multi-app with one file. [Docker Swarm](http://learning-continuous-deployment.github.io/dockerswarm/2015/06/07/docker-swarm/) helps to cluster your application in a distributed environment. It pools several Docker Engines and exposes them as a single virtual Docker engine. 

##Final words and conclusion
Docker is more lightweight than VMs and offer therefore a minimal overhead and resource usage. This makes it cheaper as well because you can run several Docker containers on only one VM so you do not need several VMs. Another point is the speed of starting a container. It is good for Sandboxing purposes and with its containers it provides modularity as well as portability and consistency. Soon we will upload our presentation to this post. 

Our personal achievements in this project were to work effectively in a team with GitHub. We understood Jenkins and Container Virtualization. Our sample projects helped us to dig deeper. 

This post is under construction...
![This post is under construction]({{site.url}}/assets/images/construction.jpg)