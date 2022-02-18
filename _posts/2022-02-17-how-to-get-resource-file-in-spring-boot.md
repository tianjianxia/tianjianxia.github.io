---
layout: post
title: How to get resource file in Spring Boot
tags: [Spring Boot, file stream, jar]
---

I recently encounter an issue when I try to deploy my Spring Boot service into Docker image. The service contains a logic that fetch files in the main/resources folder, I initially using **ResourceUtils** class(Spring Boot utility class) to getFile then receieve a File object. It runs properly on the debugging environemnt. But after packaging stages, when running the jar executes, the file fetching method throws **FileNotFound** exception. 

I digged into the jar file, found that all my resources are compiled and packaged into **"\BOOT-INF\classes\"** folder. Truth is, in debugging mode, Maven will package all the resource file into **"\target\classes\"** folder, which is refered as 'classpath' in Spring Boot utilities.

To overcome this problem, first, ask Maven to include all the resource files into packaging stage.

```xml
<build>
    <resources>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.*</include>
        </includes>
        <filtering>false</filtering>
    </resources>
</build>
```

Then, use class **ClassPathResource** and **FileInputStream** instead.

```java
ClassPathResource resource = new ClassPathResource("static/video/v1.mp4");
InputStream fis = resource.getInputStream();

// Dont try to call getFile() function, 
// it will lead to exception
File f = resource.getFile();
```

The getFile() function is a high level caller of **ResourceUtils.getFile()**, it will directly fetch **"\target\classes\"**, and throw a FileNotFound exception. But **getInputStream()** will build a stream that copy file from configuration path to the current memory stack, whose path is **"\BOOT-INF\classes\"**.

After switching **File** to **InputStream**, the exception has gone and my service run properly.