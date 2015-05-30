---
layout: post
title:  "Security aspects"
date:   2015-05-09 17:18:00
categories: docker security
banner_image: docker_security.jpg
comments: true
author_name: Steffi
excerpt_separator: <!--more-->
---

Nowadays security is an important issue when it comes to software. That's why we want to provide you with a general post and give you an overview on some security issues of Docker. 
<!--more-->

##LXC
As mentioned in our previous posts, Docker is based on LXC. Therefore it provides the security mechanisms of control groups (cgroups) and namespaces. To provide resource management features use [cgroups](https://www.kernel.org/doc/Documentation/cgroups/cgroups.txt) that associate a set of tasks with a set of parameters for one or more subsystems. Furthermore they help to ensure that each container gets the same share of memory, CPU and disk I/O (unless containers have different priorities. By default they are all equal to each other). Namespaces provide sandboxing to containers which means it is a quite straightforward form of isolation. So when you start a container with `docker run`, Docker uses `lxc-start` to execute the container. This creates not only a set of namespaces and cgroups for it, but also its own network stack, thus a container doesn't get any privileged access to sockets or interfaces of another container so it can not affect (or even see) the behaviour of another container from another namespace or host. 

##Docker daemon
The Docker daemon is responsible for taking care of creating and managing containers and is important when it comes to security because it currently requires `root` privileges. Docker allows you to share a directory between the Docker host and a guest container - you can do so without limiting the access rights of the container. Therefore you should make sure that only trusted users can be allowed to control your Docker daemon. Furthermore you should be careful with parameter checking if you run Docker from a web server because a malicious user could pass parameters to create containers on your system. Docker itself has improved the security by changing the REST API endpoint from a TCP to a UNIX socket due to the possibility of cross-site-scripting-attacks if you run Docker on a local machine.

##Kernel Capabilities
Docker containers are started with a very restricted set of capabilities, so even if an intruder manages to use the `root` command within a container, it will be harder to do serious damage. In most cases containers do not need `root` privileges at all, so they can run with a reduced capability set by denying single operations like access to raw sockets, access to filesystem operations or mount operations. In comparison to an average server Docker containers are more convenient regarding hardware management (becomes irrelevant), network management (should happen outside of the containers) or SSH access (typically managed by a single server running in the Docker host). 


##Best practices 
As a conclusion one could say that Docker containers are quite secure by default. Nevertheless you should be aware of possible threats or attacks and therefore ensure that your system is protected. Here are some best practices for your use with Docker containers. Nevertheless if you are interested in further reading, check out the [Docker website](https://docs.docker.com/articles/security/) or their [Blog](http://blog.docker.com/2013/08/containers-docker-how-secure-are-they/).

- run docker daemon in a dedicated server
- take care of running processes as non-privileged
- only trusted users should be allowed to control your Docker daemon
- if running Docker on a server move all other services within containers controlled by Docker 



