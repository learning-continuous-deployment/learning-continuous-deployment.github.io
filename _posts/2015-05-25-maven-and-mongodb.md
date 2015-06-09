---
layout: post
title:  "Maven and MongoDB: a short introduction"
date:   2015-05-25 13:15
categories: maven mongodb
banner_image: maven_mongodb.jpg
comments: true
author_name: Steffi
---

Hello! This post provides a short introduction in Maven and MongoDB and is for those who are unfamiliar with these tools. We will use both tools for our new sample project to show how to work with persistent data in Docker.

<!--more-->

##Maven

[Apache Maven](https://maven.apache.org/index.html) can be understood as a project management tool with best practices for your projects, so that several projects work in the same way and share the same characteristics. Therefore it applies patterns to the project's build infrastructure like e.g. visibility, reusability, maintainability and comprehensibility. These patterns can be used for managing Builds, Documentation, Reporting, Releases and for many more tasks. Maven's standard conventions help to accelerate the project's development process. The most important file of a Maven project is `pom.xml` (pom stands for Project Object Model) which contains all relevant information of your project. A good starting guide for Maven can be found [here](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).

Your Dockerfile should be located in the POM-file's directory (root of your project). Use the Maven image:

    FROM maven:3.2-jdk-7-onbuild
    CMD ["do something"]

The build will copy `. /usr/src/app` and `RUN mvn install` so you can build and run the image with:

    docker build -t maven .
    docker run -it --name maven-script maven

If a complete Dockerfile is inconvenient for your project you can run the Maven Docker image directly:

    docker run -it --rm --name maven-project -v "$PWD":/usr/src/maven-project -w /usr/src/maven-project maven:3.2-jdk-7 mvn clean install

For our purpose it is not necessary to create a Maven Container, so we will just start the job via Jenkins and use its Maven Plugin to build the application through the pom-file. 

###Installation

Download bin.zip and follow the instructions to install [Maven](http://maven.apache.org/download.cgi). Verify your installation with `mvn --version`. If you get an error check whether you set your `JAVA_HOME` variable correctly or wheter your `settings.xml` consists of the right configuration for you.
In the next step you could try to build your first sample project with the help of the Archetype Plugin and its command `archetype:generate` or try to compile our sample project from [GitHub](https://github.com/learning-continuous-deployment/java-mongodb-sample) with using `mvn compile` in the same directory as the project.


##MongoDB

MongoDB is a document database in which each record is a document that is similar to a JSON object which consists of field and value pairs. As you can see the values can include other array, documents or even arrays of documents.

    {
    name: "Docker",
    projects: ["Django", "MongoDB"],
    comments: [
      {
         message: 'First comment',
         dateCreated: new Date(2015,5,25)
      },
      {
         message: 'Second comment',
         dateCreated: new Date(2015,5,25)
      }
    ]
    }

Furthermore MongoDB provides high performance, e.g. with faster queries through indexes. Its replication facility provides automatic failovers and data redundancy and the horizontal scalability allows automatic sharding across a cluster of machines.

Based on the latest version of Ubuntu from the [Docker Hub Repository](https://registry.hub.docker.com/_/ubuntu/) you can import the MongoDB public GPG key, create a MongoDB repository file, whereupon you can update your packages and install MongoDB:

    # Format: FROM    repository[:version]
    FROM ubuntu:latest
    # Installation: Import MongoDB public GPG key AND create a MongoDB list file
    RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv 7F0CEB10
    RUN echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/sources.list.d/10gen.list
    # Update apt-get sources AND install MongoDB
    RUN apt-get update && apt-get install -y mongodb-org

Because MongoDB requires a data directory you need to create that with:

    # Create the MongoDB data directory
    RUN mkdir -p /data/db
To run `mongod` inside the containers launched from the MongoDB image you need to set an `ENTRYPOINT` and ports:

    # Expose port 27017 from the container to the host
    EXPOSE 27017
    # Set usr/bin/mongod as the dockerized entry-point application
    ENTRYPOINT usr/bin/mongod

Now you can save the file and build your image.


##Installation

To install MongoDB follow these [instructions](http://docs.mongodb.org/manual/installation/) and mind the necessary dependencies. 
