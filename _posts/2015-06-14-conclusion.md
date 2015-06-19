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

Firstly we want to provide some information about Conainerization in general, whereupon we go straight to Docker's technology, its use cases and our projects. Last but not least we will show some tools and complex workflows and give you our final conclusion in the end. 

Please mind that this post is still under construction, sorry... 
![This post is under construction]({{site.url}}/assets/images/construction.jpg)

##Containerization in general

Docker offers a container based solution for virtualization. It encapsulates the application and its dependencies into a system and language-independent package, whereas your code runs in isolation from other containers but share the host's resources. Therefore you do not need a VM with an entire guest OS (overhead), but only your container to run your app. 

![Virtual Machines vs. Docker]({{site.url}}/assets/images/vm-docker.png)

##Docker's Technology
Docker is based on Linux Containers (LXC) to provide its leightweight virtualization technology. 

![Kernel features]({{site.url}}/assets/images/kernel-features.PNG)

Namespaces help to isolate a workspace, so changes of a global resource are only visible for processes that are part of this Namespace. Cgroups help to distribute the available hardware resources (CPU, I/O, memory) to the processes and manages them. This abstraction layer simplifies the work with containers and its management which leads to portabilty, so you can easy share containers with machines. The reuse of containers is another important principle as well as the their version management.

The Docker Engine is the virtualization technology. Docker uses a client-server architecture in which the user interacts with the Docker daemon (client interface). The daemon is responsible for build proccesses, running containers and their distribution. 

##Docker's use cases
The slogan *build once, run anywhere* indicates many use cases for Docker application. It supports Continuous Integration as well as Continuous Delivery and Continuous Deployment. In our project we used Docker to provide a Continuous Deployment pipeline with Jenkins and GitHub. 

It is possible to use Docker containers for local development so it allows you to run GUI application inside a Docker container, e.g. NetBeans with all necessary dependencies. So you can share the container with other developers without any further setup. This speeds up and simplifies the process. 

Docker offers many advantages especially for DevOps. It eases the workstation deployment due to many application environments (dev, QA, production) and abstracts the complexity of configuration management. It can be combined with other infrastructure tools, e.g. Puppet or Chef. Docker is not supposed to replace these tools, so it easy to integrate Docker in these tools, e.g. you have an infrastructure based on Chef that builds your application and deploys it, you can use its cookbooks to the same thing in Docker, but with Docker's specific advantages. But if you don't use any other configuration management tools, you can simply rely on Docker. Furthermore Docker offers a great way to share, so Ops can produce Docker images - if necessary and Devs could help newbies to bootstrap. 

##Testing
For our Continuous Deployment pipeline the idea was that Jenkins commands the testing infrastructure to different docker applications - that run simultaneously and run tests in parallel. Why should you not just use Jenkins though? Because Docker provides you a clean environment for every fresh build and increases the speed of test cases (quick and easy to start a container). Because your application is *frozen* within a container it will always run as tested. Furthermore it is easy to switch to another version, e.g. if some dependencies have changed or you want to update or downgrade which will be achieved by simply using another container. Last but not least it is cheaper than running tests on several VM's. 

##Our continuous deployment workflow
Our [first project](http://learning-continuous-deployment.github.io/) showed how to trigger a Jenkins job by a GitHub push that creates images and containers. The new containers will be pushed to our Docker server that runs the container.

##Docker and persistence
Our [second project](http://learning-continuous-deployment.github.io/docker/images/dockerfile/database/persistence/volumes/linking/container/2015/05/29/docker-and-databases/) showed how to achieve persistence in Docker containers by using [Volumes]((http://learning-continuous-deployment.github.io/docker/container/volumes/2015/05/22/persistent-data-with-docker/)). Volumes can be used to mount directories or files from your host machine inside a container. It can be used to create dir which exists as a 
normal* dir. 

##Tools and complex workflows 
Regarding Orchestration it is necessary to have a convenient management of a large number of containers, so to manage applications across multiple containers and hosts. There are several tools from Docker to orchestrate your application. [Docker Compose](http://learning-continuous-deployment.github.io/dockercompose/multi-app/2015/05/30/docker-compose/) allows you to run your stack with only one command and describe your multi-app with one file. [Docker Swarm](http://learning-continuous-deployment.github.io/dockerswarm/2015/06/07/docker-swarm/) helps to cluster your application in a distributed environment. It pools several Docker Engines and exposes them as a single virtual Docker engine. 

##Final words and conclusion
We almost reached the end of our project, so we'd like to give you our final conclusion! We hope you enjoyed reading our blog. 

###Advantages of Docker
Docker is more lightweight than VMs and offers therefore a minimal overhead and resource usage. This makes it cheaper as well because you can run several Docker containers on only one VM so you do not need several VMs. Another point is the speed of starting a container. Running multiple containers on one host is easy and the containers itself are isolated from each other. It offers many advantages for DevOps such as sharing of containers, e.g. for a local development with a GUI for a container. The public repository DockerHub offers many pre-built images that you can use for your own purposes.

###Advantages of Dockerized Apps
Regarding dockerized apps Docker offers an easy versioning of the images, so you can simply upgrade / downgrade / switch to another version if necessary (e.g. if some dependencies have changed). 

The immutable infrastructure of Docker provides abstraction, consistency and modularity. With immutable infrastructure we mean that the configuration state of an application won't be cahnged, neither in development, testing or production, so you will always have the same state, whereas the abstraction separates the operating system and its dependecies from the application and its dependencies. The provided consistency ensures that the tested code will be the code that will be eventually deployed and used in the productive system, so we can speak of *run as tested*. 

Another advantage of dockerized apps is the flexibility and portability, so that the software can run in different environments and isn't bound on any specific hardware or services. The flexibility allows to run many instances of the same application. 

###Disadvantages
Personally we could not find many disadvantages of Docker. But because there are many beta tools so far (e.g. Swarm), orchestration needs to be provided by other tools to make your application scalable. Docker has got a big growing ecosystem, so it can take a while for you to find the right tools to combine Docker with. Although Docker is easy to learn, we needed some time to be really into it. 

###Our conclusion
The importance of virtualization technologies grows with an increasing speed. Containerization gains more and more attention because companies want to develop software faster, cheaper and better. Considering continuous integration, delivery or deployment, DevOps need to choose the right way for their own use case. 

Docker is a great tool and supports the continuous deployment pipeline. It's got a good integration in already existing configuration managment tools. With its big and growing ecosystem it offers many use cases and we can be excited for more things to come! 

###Our personal achievements
Our personal achievements in this project were to work effectively in a team with GitHub. We understood Jenkins and Container Virtualization with containerized applications, multi-apps and Volumes. Our sample projects helped us to dig deeper and we got some insights in the continuous deployment pipeline. 

###Useful Sources

   - Docker User Guide: https://docs.docker.com/
   - Tutorial basic Docker commands: http://www.docker.com/tryit/ 
   - Docker Plugin: https://wiki.jenkins-ci.org/display/JENKINS/Docker+Plugin 
   - GitHub Markdown: http://dillinger.io/
   - Cheat-Sheet: https://github.com/wsargent/docker-cheat-sheet#containers 
   - S. Holla: Orchestrating Docker; Packt Publishing; UK; January 2015; ISBN: 978-1-78398-478-7. 
   - J. F. Smart: Jenkins: The Definitive Guide; O’Reilly Media, Inc.; US; 2011; ISBN: 978-1-449-30535-2.