---
title: Getting started with Spring boot
date: 2024-05-13 10.10PM
categories: [spring boot]
tag: [java , spring boot]
image:
  path: /assets/images/spring_boot.png
---
# Getting started with Spring Boot

Spring Boot is a Java framework used for backend development. It's popular for enabling the rapid development of production-ready web applications with minimal configuration.

Spring Boot provides numerous built-in libraries for various technologies such as messaging, security, etc., making it exceptionally easy to use. While it's commonly utilized in microservice architecture, it's also applicable in monolithic architecture setups.

There are several advantages to using Spring Boot that contribute to its status as a leading framework:

1. **Auto-configuration**: By simply adding the .jar file as a dependency in our Spring project, Spring handles most configurations automatically. If needed, we can override or exclude auto-configuration for specific libraries. For example, configurations for web servers, security, databases, etc., are managed effortlessly.

2. **Starter dependencies**: Spring Boot offers many starter dependencies, simplifying the integration of new technologies. This avoids the need for manual integration.

3. **Actuator support**: In complex applications, monitoring can be challenging. Spring Boot addresses this with built-in support called Actuator. Actuator provides endpoints to check the health of the application, facilitating easier monitoring, especially in applications integrating with various third-party services and databases.

4. **Embedded web servers**: Spring Boot includes embedded web servers, making it effortless to develop and deploy applications. Without embedded servers, deploying to external web servers would require writing extensive XML configurations.

5. **Large community**: Spring Boot boasts a vast community, which significantly eases the developer's life. If stuck, there's ample support available.

Setting up your first Spring Boot project is straightforward:

- Open your favorite browser and search for "Spring Initializer" (https://start.spring.io/).

- Fill in the group, artifact name, package name, select packaging type as JAR, and choose dependencies like Spring Web.

![A screenshot](/assets/images/spring_io.png)

- Click "Generate Project" to download the zip file, which contains your project. Open it in your preferred IDE (e.g., STS, IntelliJ).

Once your project is open, you'll see the following folder structure:

![A screenshot](/assets/images/spring_folder_structure.png)

- **src**: Divided into two subfolders: "main" and "test".
    - **main**: Development code resides here. Configuration files and resources can be added to the "resources" subfolder.
    - **test**: Contains all test code.
- **pom.xml**: This file manages dependencies of our app and their versions.

The entry point of the application is the class annotated with `@SpringBootApplication`, which contains the main method:

```java

package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}

```
**@SpringBootApplication** indicates that Spring scans all packages of this class and its subclasses for configurations and components. It also enables auto-configuration, scanning all dependencies added in the pom.xml file.

## To print "Hello, World!":

- Create a package under `src/main` named "controller".

- Create a Java class named `DummyController.java` under `src/main/controller` and annotate it with `@RestController` (indicating to Spring Boot that it will handle web requests and responses).

- Write a simple HTTP GET request that returns a string, such as "Hello, World!".

### ref:

![A screenshot](/assets/images/DummyController.png)
 