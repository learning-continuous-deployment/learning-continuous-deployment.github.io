---
layout: post
title:  "Maven and MongoDB: a short introduction"
date:   2015-05-25 13:15
categories: docker jenkins git
banner_image: maven_mongodb.png
comments: true
author_name: Steffi
---

Hello! This post is for those who are unfamiliar with Maven and MongoDB and provides a quick introduction in those tools. We will use both tools for our new sample project to show how to work with persistant data in Docker.

<!--more-->

##Maven

[Apache Maven](https://maven.apache.org/index.html) can be understood as a project management tool with best practices for your projects, so that several projects work in the same way and share the same characteristics. Therefore it applies patterns to the project's build infracstructure like e.g. visibility, reusability, maintainability and comprehensibility. These patterns can be used for managing Builds, Documentation, Reporting, Releases and for many more tasks. Maven's standard conventions help to accelearte the project's development process. The most important file of a Maven project is `pom.xml` which contains all relevant information of your project. A good starting guide for Maven can be found [here](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html). 

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

Futhermore MongoDB provides high performance, e.g. with faster queries through indexes. Its replication facility provides automatic failovers and data redundancy and the horizontal scalability allows automatic sharding across a cluster of machines. 
 

##Installation

To install MongoDB follow these [instructions](http://docs.mongodb.org/manual/installation/) and mind the necessary dependencies. 