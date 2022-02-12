---
layout: post
title: A debug process in AWS EBS
tags: [AWS, Spring Boot, EBS, Maven]
---

I encounter a problem when deploy my Spring boot jar to AWS EBS service.

The service including several api controllers and it use MongoDB as repository.

The issue is when the EBS instance is created. It returns an error "web.service web.pid failed with error:open /var/pids/web.pid: no such file or directory"

This shows the problem that the JVM process cannot boot because it is missing an important part from linux command "web.service".  

Based on AWS documents, "web.service" is the linux service for Beanstalk which is responsible of activating the virtual environment in staging and running the Tomcat server. So let's go through how tomcat container boot an actual web server from scartch.

First, a jar file of Spring Boot application is running from the starter class "AppCoreStarter" with annotation "@SpringBootApplication". Before this, the system should know which class is the starter class. I am using Maven as the package manager. In Maven, you can specify a Manifest starter class using property "configuration.archive.manifest.mainClass". 

<br/>

Or, when running jar file, we can use 'cp' command to specify a package to scan the main class. Like this: "java -jar -cp 'apiCore.MainClassStarter'". 

<br/>

But in AWS EC2(the actual instance of EBS), the service transfer jar file into a 'web.service' module. And directly run the jar file from that virutal environment. So, the instance do not know which starter class should it start with. 

So the problem is missing the mainClass configuration in Maven pom.xml. We can dig into AWS log "web.stdout.log" and find "web: no main manifest attribute, in application.jar". We add the manifest configuration then re-deploy, the service runs properly.

From this dubugging process, we learn that when using Spring Boot, you need specify the starter class by Maven configuration or using command. And using Spring Initializr(it init the Maven configuration automatically by default) is recommanded.