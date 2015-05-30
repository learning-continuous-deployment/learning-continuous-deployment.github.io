---
layout: post
title: "Docker and Databases - a Hands-on Guide"
date: 2015-05-29 13:02:00
categories: docker images dockerfile database persistence volumes linking container 
banner_image: docker_database.jpg
comments: true
author_name: Jan
---
So lately you read about volumes, sharing data and achieving persistence when working with ephemeral Docker containers. In the following blogpost we use this knowledge to build a dockerized web application. You will learn how to compose the single tiers and how to to link the single containers together.
<!--more-->

##Introduction 
The task is to *containerize* a web application using Docker and achieve persistence with ephemeral containers. For this purpose we created a sample project to illustrate that, the pitfalls and the solution. The source code can be found [on Github](https://github.com/learning-continuous-deployment/java-mongodb-sample). You will recognize, that the sample is super basic, because we wanted to minimize distractions. It uses Java for the business logic and the connection to a MongoDB, which functions as datastore.
All descriptions, path names and so on refer to the used technologies, but we tried to keep it as unspecific as possible.

##Solution Idea
As already mentioned in the blogpost about volumes it is considered to be *best practice* to use a data-only container to store our database entries. So it would be enough to have two containers - one with the data and another one running the DBMS and the actual application (an example for this setup can be found [here](https://github.com/jbfink/docker-wordpress)). But for our use case this is not enough, because we want the maximum flexibility and therefore consider, that *multiple applications* should be able to access the database. This leads to the following division in containers: 

 1. __Data-only container__, which  references the volume [/data/db](http://docs.mongodb.org/manual/tutorial/manage-mongodb-processes/) (the default directory for storing data)
 2. __DB-Container__ running [mongod](http://docs.mongodb.org/manual/reference/program/mongod/), (the primary deamon process of MongoDB)
 3. __Application container__ running the business logic

This enables a separation of duties in a neat way and improves portability, reusability, maintainability and so on. 
At the same time this division in separate containers brings up another problem. The containers have to be enabled to *communicate* with each other. But we will deal with that later.

###A detailed look on the containers
Before we can deal with the linkage and deployment of the containers, we need to understand them and therefore have a detailed look on the [images](https://docs.docker.com/userguide/dockerimages/) they are based on.

For the data-only container we don't have to use a Dockerfile. It functions as a plain datastore and therefore it is enough to use a base image (e.g. 'ubuntu') and create the volume via the `-v` - flag. 
    
    docker create -v /data/db/ --name data_container ubuntu
    
This will create a container named `data_container`, which references the volume `/data/db`. This volume can be imported - actually used - by other containers through the `--volumes-from`- flag.  
These other containers are a bit more complex. Thus their images are build from Dockerfiles. The Dockerfile for the DB-container has the following form and content:
    
    FROM ubuntu:latest
    # Installation of MongoDB:
    # Import MongoDB public GPG key AND create a MongoDB list file
    RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv 7F0CEB10
    RUN echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee       /etc/apt/sources.list.d/10gen.list

    # Update apt-get sources AND install MongoDB
    RUN apt-get update && apt-get install -y mongodb-org

    # Expose port 27017 from the container to the host
    EXPOSE 27017

    # Set the mongod daemon as entry-point application
    ENTRYPOINT /usr/bin/mongod

So nothing special here. The latest Ubuntu image serves as base and then the MongoDB is installed. The two most import things to recognize are that `mongod` is started when running the container and that the [27017](http://docs.mongodb.org/manual/reference/default-mongodb-port/) port is exposed, which is the default port for mongod instances. One has to notice, that it is necessary to run the resulting image with the `--volumes-from` - flag to the data-only container to have access to the volume containing `/data/db`: 
    
    docker run -d -p :27017 --volumes-from data_container --name mongodb mongodb_$BUILD_ID
    
The last Dockerfile to examine is the one of the actual application:
    
    FROM library/java:openjdk-8-jre
    
    COPY ./target/JavaMongoDbApp-0.0.1-SNAPSHOT-jar-with-dependencies.jar /home/code/
    EXPOSE 8080
    ENTRYPOINT ["java", "-jar", "/home/code/JavaMongoDbApp-0.0.1-SNAPSHOT-jar-with-dependencies.jar"]

This time we use the Dockerhub to pull an image, where the OpenJDK Java 8 Runtime Environment is already installed. Then we copy the .jar-file with all its dependencies to the `/home/code`directory in the container, expose port number 8080, on which the HTTP server of the application runs and finally set the entry point to run the .jar-file.  

So far so good, but when someone tries to run this application container he will get an `com.mongodb.MongoSocketOpenException: Exception opening socket`, because the application tries to access port 27017 on the localhost, where he expects the `mongod` by default.  
So it is still crucial to introduce one container to the other and enable them to *communicate*.  

###Solutions for linking
This procedure is called __linking__ and is not as hard as you might expect. Actually there are two possible ways to achieve dataflow between two or more containers.
1. Use exposed IP ports and connect via them. 
2. Use `--link` - flag in recent docker versions.  

The second option also uses exposed ports, port forwarding and such things, but Docker takes care of the *dirty* work with network interfaces and so on. General and extensive information about linking containers can be found [in the documentation](https://docs.docker.com/userguide/dockerlinks/). But we will cover all necessary steps in the following sections.

####Linking via `--link`
As already mentioned this is the cleanest way of achieving the connection of two services. 
By specifying the `--link` - flag in the `run`-command Docker sets up environmental variables for the exposed ports. This allows the second container to dynamically adjust its networking settings without explicit port forwarding or fiddling around with IPs. By specifying `--link` with the parameters `<name of running container>:<alias`> we establish such a link. 

    docker run -d -p 80:8080 --link mongodb:mongo --name app app_$BUILD_ID
    
This command runs the `app_$BUILD_ID` image and links the running container `mongodb` as `mongo`. This causes Docker to create a bunch of the already mentioned environment variables. These variables are named in a certain way. The specified alias - in our case `mongo` - serves as prefix in capitals followed by an underscore. (For more information [see the Docker documentation in the section *Environment Variables*](https://docs.docker.com/userguide/dockerlinks/))  

For the connection to the database only one environment variable is of interest. `<ALIAS>_PORT` specifies the IP address of the second/linked container and the exposed port. This is enough to connect the Java application to the `mongod` daemon and therefore manipulate the database. This is done by reading that environment variable in the Java code and setting the MongoClient to the specified IP and port. In Java code this can look like this: 
    
    //Specifies the name of environmental variable set by Docker --link
    private final static String DB_ENV_NAME = "MONGO_PORT";
    
    //Takes care of internal connection pooling
    private static MongoClient mongoClient;
    
    private static String getDatabaseEnv() {
		Map<String, String> env = System.getenv();
		return env.get(DB_ENV_NAME);
	}

    private static void initMongoClient() {
		String address = getDatabaseEnv();
		
		if (address != null) {
			LOGGER.verbose(String.format("Connecting to database: %s",
					addres
			//hack of the protocol identifier (tcp://)
			address = address.substring(6);
		
			//actual connection to the database
			mongoClient = new MongoClient(address);
		}
		[...]
	}

###Final words
This is an illustration of a fully working example of a *dockerized* web application with a MongoDB as persistent data storage. Each functionality is encapsulated and can be easily exchanged and updated. Like in the last sample project we created an automated deployment workflow, which is super convenient and fast, where a `git push` triggers the build and deployment of the application container. 


###References:
* https://blog.codecentric.de/en/2014/01/docker-networking-made-simple-3-ways-connect-lxc-containers/
* http://stackoverflow.com/questions/19948149/can-i-run-multiple-programs-in-a-docker-container 
* https://docs.docker.com/userguide/dockerlinks/
* https://docs.docker.com/userguide/dockervolumes/
* http://howchoo.com/g/zdq5m2exmze/docker-persistence-with-a-data-only-container
* https://docs.docker.com/userguide/dockerlinks/
