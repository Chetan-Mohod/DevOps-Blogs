---
title: "Building a Banking App? Here’s How I Dockerized Mine in 5 Minutes Flat!"
datePublished: Wed Nov 27 2024 16:34:34 GMT+0000 (Coordinated Universal Time)
cuid: cm403w2gu000509lcco5vcpjf
slug: building-a-banking-app-heres-how-i-dockerized-mine-in-5-minutes-flat
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1732725144143/65c1b235-cc73-4c49-be51-5b0fd7d7eb69.webp
tags: docker, containerization, springboot, spring-framework, dockerfile, techblog, docker-compose, programming-tips, docker-images, 90daysofdevops, multistage-docker-build

---

This is a Java-based application utilizing the Spring Boot framework, which will be used to create our Banking App. The application is integrated with a MySQL database.

> I have added this project on my: [GitHub Repo](https://github.com/Chetan-Mohod/Springboot-BankApp) you can fork this project.

### PRE-REQUISITES FOR THIS PROJECT:

* AWS Account
    
* AWS Ubuntu EC2 instance (t2.medium)
    
* Install Docker
    
* Install docker compose
    

Let's begin.

I will guide you through this project step by step. Please follow the steps below:

1. Go to AWS console and create EC2 instance with `t2.medium` instance type:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648103485/b2948b83-485c-4837-82ba-0c31bf6b416f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648156804/9cda6d79-397b-43fc-a773-171a0ca72ad2.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648182470/6c92b6f5-c54c-4530-a2b1-9b553da4c2cf.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648210286/a3568277-81a8-451e-87bd-8be8a7e9d4b4.png align="center")

2. Once our EC2 instance is created, click on "Connect," then navigate to the SSH section and connect through the terminal.
    
3. Run git clone command:
    

```bash
git clone https://github.com/Chetan-Mohod/Springboot-BankApp.git
```

4. You can list the contents of your terminal, and you will see that the Spring Boot app project is available.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648610696/bba0786e-f92e-442f-94cd-b95ec8a8cb88.png align="left")

5. Now you can remove the Dockerfile and docker-compose.yml file from the directory. Initially, these files are not provided when the developer gives us the code, so we need to create them from scratch.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648689970/a29d8452-5196-4361-9fa0-beff4cd0964a.png align="center")

This is how the developer provides us with the code:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732648802525/10508be3-a09b-450f-937f-91f4d1077457.png align="left")

Read all the instructions from `README.md`.

As a DevOps Engineer, our first task is to create a Docker image so we can run the application. Before creating the Docker image, we need to determine which base image is suitable for this Spring Boot application.

There are many images available, but our primary task is to build our Spring Boot application, which is done using Maven. Therefore, search for a "*Maven base Image docker openjdk*" on a search engine: [Maven Image](https://hub.docker.com/_/maven/).

6. Now, access the EC2 instance terminal and create a Dockerfile. Add the following code:
    

```bash
# ---------------- STAGE 1 ----------------------

# Pull base image to use Maven for building JAR files
FROM maven:3.8.3-openjdk-17

# Working directory where your code and JAR file will be stored
WORKDIR /app

# Copy all the code from the local host to the container
COPY . /app

# Build the app to generate the JAR file
# Maven will install all libraries as per pom.xml
#"-DskipTests is used to skip test cases" if things are not ready at your end then use.
RUN mvn clean install -DskipTests=true

# Expose the port to map it with the host
EXPOSE 8080

#Execute the JAR file using java command and -jar flag (to specify we're executing a jar file)
#Now after installing mvn we have .jar file, now we need to run this jar file
ENTRYPOINT ["java", "-jar", "/bankapp.jar"]
```

7. Since this is a new system, we will proceed by installing Docker and updating the system.
    

```bash
#To update the system
sudo apt-get update

#to install docker
sudo apt-get install docker.io -y

#To give permission to our user for docker grp
sudo usermod -aG docker $USER && newgrp docker

#Check docker containers
docker ps
```

8. Now, let's proceed to build our Dockerfile:
    

```bash
docker build -t bankapp .
```

9. To view the list of Docker images available on your system, execute the following command:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732685260720/a426322f-8e72-4244-99d7-cd8b61141b48.png align="center")

Here, you can observe that the Maven image size is 785MB, which is quite large. To address this, we will use Multi-Stage Docker builds to reduce the size of this image.

10. Add this code to your `Dockerfile` to reduce image size:
    

```bash
# ---------------- STAGE 1 ----------------------

# Pull base image to use Maven for building JAR files
FROM maven:3.8.3-openjdk-17 AS builder

# Working directory where your code and JAR file will be stored
WORKDIR /app

# Copy all the code from the local host to the container
COPY . /app

# Build the app to generate the JAR file
# Maven will install all libraries as per pom.xml
#"-DskipTests is used to skip test cases"
RUN mvn clean install -DskipTests=true

# ---------------- STAGE 2 ----------------------

FROM openjdk:17-alpine

WORKDIR /app

#Stage o/p stores in target directory
COPY --from=builder /app/target/*.jar /app/target/bankapp.jar

# Expose the port to map it with the host
EXPOSE 8080

#Execute the JAR file using java command and -jar flag (to specify we're executing a jar file)
#Now after installing mvn we have .jar file, now we need to run this jar file
ENTRYPOINT ["java", "-jar", "/app/target/bankapp.jar"]
```

Now you can see that the image size has been reduced.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732686298031/7d5ff134-f845-45f9-8ae5-65f5795f9af5.png align="center")

11. Before proceeding further, we need to create a network.
    
    * Why create a network? It allows communication between the two containers (bankapp and mysql).
        

```bash
docker network create -d bridge bankapp
```

11. Now, let's create a MySQL container:
    
    * When setting up a database container, it is essential to consult the developer for the names of the environment variables. This information is available in the `application.properties` file located in our Git repository. These properties are use to run the application.
        
    * When you pass env variables in application container then that variables are mapped with `application.properties`.
        

```bash
docker run -itd --name mysql -e MYSQL_ROOT_PASSWORD=Test@123 -e MYSQL_DATABASE=BankDB --network=bankapp mysql
```

12. Now, let's create the Application Container:
    

```bash
docker run -itd --name BankApp -e SPRING_DATASOURCE_USERNAME="root" -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/BankDB?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC" -e SPRING_DATASOURCE_PASSWORD="Test@123" --network=bankapp -p 8080:8080 bankapp-mini:latest
```

13. Now we can check the logs:
    

```bash
docker logs container_id
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732689373752/430b8552-9cec-4fb8-8806-5061192710b3.png align="center")

14. Now navigate to your AWS instance, then go to the Security Group settings. Edit the Inbound Rules and add an entry for port 8080, allowing traffic from Anywhere (IPv4).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732689518074/eee2ef7b-ff81-441c-afb3-0b54ca9a8238.png align="center")

15. Now, copy the public IP of your instance and paste it into your browser, followed by port 8080.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732719206023/1dfde5a1-de2f-484a-b7a2-5b8c4f93fe3a.png align="center")

16. You can register new users by adding them to the system:
    
    * After registering some users, proceed to log in:
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732719456055/402208bf-e313-4b4f-bd75-0ebc26e5d9e0.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732719511060/687ebb01-74d1-4563-9864-beff07b979ff.png align="center")

* Now Deposit 50k in User1 account
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732719654946/3c83699b-2e0c-4d20-aa0b-2f5f743d3ad4.png align="center")

* To transfer 10k to User2, follow these steps:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732719709872/60810f7d-d353-426f-ac6c-835642022f0d.png align="center")

* Click on **Transaction**:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732719800277/3437e79b-7638-4e4a-abd5-49934c0b1ec7.png align="center")

This is the process for building an application using Multi-Staging Docker.

In the upcoming blog, I will explain how to build this application using Docker Compose.

### Interview Question:

1. CMD vs ENTRYPOINT?
    
    * **CMD**: This instruction allows you to specify default commands that can be overridden when the container starts. It provides flexibility to change the command or arguments at runtime.
        
    * **ENTRYPOINT**: This instruction sets a fixed command that cannot be overridden at runtime. It ensures that the specified command is always executed when the container starts.
        

### Happy Learning :)

**Chetan Mohod**

For more DevOps updates, you can follow me on [LinkedIn](https://www.linkedin.com/in/chetanmohod/).