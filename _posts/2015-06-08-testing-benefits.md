---
layout: post
title:  "Testing: Benefits of Docker"
date:   2015-06-08 10:00
categories: Docker Testing
banner_image: testing.jpg
comments: true
author_name: Steffi
---

Sufficient testing is probably one of the most important issues in the software development life cycle. This post shows you the benefits of running test cases with Docker.  
<!--more-->

##Benefits

Jenkins test suite is quite comfortable and provides flexibility and many use cases. Its GitHub integration is a great feature. But running test cases only on Jenkins could interfere with the desired speed of testing each case. Here comes again Docker's advantage. With Docker you test the deployment of your app in a clean environment, for every fresh build. Furthermore it is cheaper than running several tests on several virtual machines, e.g. EC2, especially because you will not need all resources of one VM. With Docker you run several tests on several containers, but on one VM - without much overhead. Another advantage is Docker's speed in starting containers. 

When it comes to upgrading, downgrading or switching to another version due to any other dependencies or version problems, Docker can help within seconds. With a Docker container you can switch back and just use the previous image. 

##Idea

So far the general idea is to trigger a Jenkins job with each GitHub push. With pulling the corresponding images from DockerHub, Jenkins builds the desired containers. In our case it sends the built .tars to our other server. The same procedure applies to test cases. Jenkins can command your testing infrastructure to different docker applications simultaneously and run your tests in parallel.

Since your application is then frozen in a container, it will run as tested, no matter where it is deployed. So if there are any new dependencies in your application, you can upgrade this component in the container and go through the life cycle gain: test the result of the container and deploy the new image to your hosts. 

