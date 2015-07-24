---
layout: post
title:  "Docker in a nutshell: our conclusion"
date:   2015-06-14 12:00
categories: Docker Conclusion
banner_image: retrospection.jpg
comments: true
author_name: Jan, Steffi, Markus, Marius
---

In our blog *Learning Continuous Deployment* we wanted to show you the general basics how to develop *dockerized* apps and the benefits of using Docker. This post summarizes our learnings and gives our final conclusion.

<!--more-->

Firstly we want to provide some information about Containerization in general, after which we go straight to Docker's technology, its use cases and our projects. Last but not least we will show some tools and complex workflows and give you our final conclusion in the end.


##Containerization in general

Containerization offers a container based solution for virtualization. It encapsulates the application and its dependencies into a system and language-independent package, whereas your code runs in isolation from other containers but share the host's resources. Therefore you do not need a VM with an entire guest OS and its overhead due to not used resources, but only your container to run your app.

![Virtual Machines vs. Docker]({{site.url}}/assets/images/vm-docker.png)

Docker is that kind of a lightweight container based solution for virtualization. Comparing the start and stop times of Docker to VMs we see a significant difference, where VMs need 30-45s to start, 5-10s to stop. Docker is in the start time about 600 times faster and in the stop time about 100 times. Both times you will only need about 50ms.

##Docker's Technology
Docker is based on some features - namely namespaces and cgroups - of the Linux Kernel. It provides Linux containers (LXC) or its own implementation libcontainer as *container formats*. So it is able to provide lightweight operating system virtualization technology.

![Kernel features]({{site.url}}/assets/images/kernel-features.PNG)

On the one hand namespaces abstract a global resource in an isolated instance and build so called isolated workspaces. Changes of a resource are only visible for processes, that are part of the same namespace. Cgroups on the other hand help to distribute the available hardware resources (CPU, I/O, memory,...) to the processes and manages them. Effective containerization is possible without the use of Docker, but Docker provides another abstraction layer, which simplifies the work with containers and its management by the usage of its engine. The Docker engine leads to portability,  so you can easily share containers with other machines, and application centric containers. Furthermore automatic building and reuse of containers other important aspects. The last but not least advantage of the use of Docker is its version management, which the utilization of the union file systems provides.

The already mentioned Docker Engine is basically all the virtualization technology and utilities. Docker uses a client-server architecture, in which the user interacts with the Docker daemon (through the commandline interface on the client). The daemon is responsible for building images, running containers and their distribution.

##Docker's use cases
As we know now, it is quite easy to create containers with Docker. The slogan *build once, run anywhere* indicates many use cases for Docker applications. It supports Continuous Integration as well as Continuous Delivery and Continuous Deployment, so to automate as much as you can in your development pipeline.

In the step *Build* you build your app with a Docker container and can use any language or any tools. It will be guaranteed that you get a clean and safe environment for your application with all its necessary dependencies and packages. The created container can be delivered anywhere in its *Shipping* process without breaking anything. So it's quite easy to ship your container to the Quality Assurance, other team members or to a Cloud. Furthermore the portability is guaranteed and the further process of test automation or integration is simplified. In the *Run* step your application is running in a Docker container where compatibility is guaranteed.

###Local development
It is possible to use Docker containers for local development so it allows you to run GUI application inside a Docker container, e.g. NetBeans with all necessary dependencies. This implies that you don't need to install everything on your machine if you need it e.g. for only one project. Furthermore you can share the container with other developers without any further setup. This speeds up and simplifies the process.

###Testing
For our Continuous Deployment pipeline the idea was that Jenkins commands the testing infrastructure to different docker applications - these run simultaneously and run tests in parallel. Why should you not just use Jenkins though? Because Docker provides you a clean environment for every fresh build and increases the speed of test cases (quick and easy to start a container). This means that you can simulate your later productive system. And because your application is *frozen* within a container it will always run as tested. Furthermore it is easy to switch to another version, e.g. if some dependencies have changed or you want to update or downgrade which will be achieved by simply using another container. Last but not least it is cheaper than running tests on several VM's because you maybe only need one VM but can run several containers with several test cases in it.

###DevOps
Docker offers many advantages especially for DevOps, so to develop and deploy software faster and in better quality. The phrase *Configure once... Run anything*  indicates Dockers advantages. With its isolation, lightweight solution and speed in building distributed systems, it eases the workstation deployment and the development lifecycle gets more efficient, consistent and repeatable.

It can be easily combined with other infrastructure tools, e.g. Puppet or Chef, whereas Docker is not supposed to replace these tools, but to integrate itself in these tools. If you already have e.g. an infrastructure based on Chef that builds your application and deploys it, you can use docker cookbooks to install docker and run containers within the Chef configuration management workflow. But if you don't use any other configuration management tools, you can simply rely on Docker. Furthermore Docker offers a great way to share, so Ops can produce Docker images - if necessary - and Devs could help newbies to bootstrap.


##Our continuous deployment workflow
Our [first project](http://learning-continuous-deployment.github.io/) showed how to trigger a Jenkins job by a GitHub push that creates images and containers. The new containers will be pushed to our Docker server that runs the container.

##Docker and persistence
Our [second project](http://learning-continuous-deployment.github.io/docker/images/dockerfile/database/persistence/volumes/linking/container/2015/05/29/docker-and-databases/) showed how to achieve persistence in Docker containers by using [Volumes]((http://learning-continuous-deployment.github.io/docker/container/volumes/2015/05/22/persistent-data-with-docker/)). Volumes can be used to mount directories or files from your host machine inside a container or to create directories, which can be shared among containers. The concept of data-only containers leads to a neat separation of duties and enables one to store persistent data.

##Tools and complex workflows
Regarding Orchestration it is necessary to have a convenient management of a large number of containers, so to manage applications across multiple containers and hosts. There are several tools from Docker to orchestrate your application. [Docker Compose](http://learning-continuous-deployment.github.io/dockercompose/multi-app/2015/05/30/docker-compose/) allows you to run your stack with only one command and describe your multi-app with one file. [Docker Swarm](http://learning-continuous-deployment.github.io/dockerswarm/2015/06/07/docker-swarm/) helps to cluster your application in a distributed environment. It pools several Docker Engines and exposes them as a single virtual Docker engine. Beware that these tools Docker provides are still in development (beta) and therefore should not be used in production yet. But there are tons of alternative implementations from third party developers out there that you could easily plug into your dev environment. If you are interested in *Compose* alternatives, check out Core OS, Amazon Web Services or Google Compute Engine. For *Swarm* i.e. Apache Mesos, Google Kubernetes or Core OS Fleet.

![Docker Swarm]({{site.url}}/assets/images/docker-swarm.png)

#Minimalistic Operating Systems
Lastly just a short note about Minimalistic Operating Systems in the Docker universe.
Instead of just using Ubuntu as base image for docker containers or host operating system, there are other distributions available - specialized for the use with containers.
Examples are: Core OS, ubuntu snappy core, project atomic, rancher OS, busybox, staticpython and much much more (just google a little or look in docker hub to find tons of them).
Most of those distributions are smaller than typical linux distros and reduce the overhead by delivering only tools that are necessary in container context. Some like Core OS even deliver clustering solutions and improved stability and security or they are indended to provide the best environment for one particular use case, such as beeing the smallest distribution that can still handle python apps. 

##Final words and conclusion
We almost reached the end of our project, so we'd like to give you our final conclusion! We hope you enjoyed reading our blog.

###Advantages of Docker
Docker is more lightweight than VMs and offers therefore a minimal overhead and resource usage. This makes it cheaper as well because you can run several Docker containers on only one VM so you do not need several VMs. Another point is the speed of starting a container. Running multiple containers on one host is easy and the containers itself are isolated from each other. It offers many advantages for DevOps such as sharing of containers, e.g. for a local development with a GUI for a container. Another big advantage is the tremendous ecosystem that has developed around docker. The public repository DockerHub offers many pre-built images that you can use for your own purpose. And there are lots of tools available that can complement your docker experience with customized solutions. It is very positive to note that most of the third party developers share dockers' idea of "openness" and provide pluggable modules that can be combined with others.

###Advantages of Dockerized Apps
Regarding dockerized apps Docker offers an easy versioning of the images, so you can simply upgrade / downgrade / switch to another version if necessary (e.g. if some dependencies have changed).

The immutable infrastructure of Docker provides abstraction, consistency and modularity. With immutable infrastructure we mean that the configuration state of an application won't be changed, neither in development, testing or production, so you will always have the same state, whereas the abstraction separates the operating system and its dependencies from the application and its dependencies. The provided consistency ensures that the tested code will be the code that will be eventually deployed and used in the productive system, so we can speak of *run as tested*.

Another advantage of dockerized apps is the flexibility and portability, so that the software can run in different environments and isn't bound on any specific hardware or services. The flexibility allows to run many instances of the same application.

###Disadvantages
Personally we could not find many disadvantages of Docker. But because there are many beta tools so far (e.g. Swarm), orchestration needs to be provided by other tools to make your application scalable. Docker has got a big growing ecosystem, so it can take a while for you to find the right tools to combine Docker with. Although Docker is easy to learn, we needed some time to be really into it.

__But__ there is a need to say that there are some serious security concerns as e.g. the possibility of escaping the 'jail'. There are many posts from Docker as well and they seem to work hard on fixing all the security concerns many people have mentioned. Another point is the problem with security bugs in existing images on DockerHub. If you use an existing image, please check it first and make sure it can't be affected by *Heartbleed* or any other bugs anymore. So at the moment you still have to be very careful to run Docker containers in production environments.

###Our conclusion
The importance of virtualization technologies grows with an increasing speed. Containerization gains more and more attention because companies want to develop software faster, cheaper and better. Considering continuous integration, delivery or deployment, DevOps need to choose the right way for their own use case.

Docker is a great tool and supports the continuous deployment pipeline. It's got a good integration in already existing configuration management tools. With its big and growing ecosystem it offers many use cases and we can be excited for more things to come!

###Our insights and personal achievements
Our personal achievements in this project were to work effectively in a team with GitHub. We setup a Jenkins server and triggered several jobs with it. Furthermoe we understood the importance of virtualization and especially containerization. Docker offers many advantages and we learned how to create containerized applications and also multi-container-apps. Our sample projects helped us to dig deeper and we got some insights in the continuous deployment pipeline and the applicability of Docker containers in cloud environments.

###Useful Sources

   - Docker User Guide: https://docs.docker.com/
   - Tutorial basic Docker commands: http://www.docker.com/tryit/
   - Docker Plugin: https://wiki.jenkins-ci.org/display/JENKINS/Docker+Plugin
   - GitHub Markdown: http://dillinger.io/
   - Cheat-Sheet: https://github.com/wsargent/docker-cheat-sheet#containers
   - S. Holla: Orchestrating Docker; Packt Publishing; UK; January 2015; ISBN: 978-1-78398-478-7.
   - J. F. Smart: Jenkins: The Definitive Guide; O’Reilly Media, Inc.; US; 2011; ISBN: 978-1-449-30535-2.
