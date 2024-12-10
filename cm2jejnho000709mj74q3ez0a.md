---
title: "Understanding Docker: Revolutionizing Application Deployment"
datePublished: Mon Oct 21 2024 19:21:03 GMT+0000 (Coordinated Universal Time)
cuid: cm2jejnho000709mj74q3ez0a
slug: understanding-docker-revolutionizing-application-deployment
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1729538013006/e6708b79-8a3d-4266-9103-b62570415546.png
tags: docker, devops, virtualization, dockerfile, docker-compose, docker-images, devops-articles, docker-network, devops-journey, docker-daemon, docker-essentials-or-docker-basics-or-docker-cli-explained, dockerd, docker-rest-api

---

Hey there! If you’ve been in the tech world for a while, you’ve probably heard of **Docker**. It’s a game-changer in how we deploy and manage applications. But what exactly is Docker, and how does it differ from traditional virtualization? Let’s break it down together.

## What is Docker?

At its heart, Docker is an open-source platform that makes it super easy to build, ship, and run applications using **containers**. Imagine a container as a lightweight, portable box that holds everything your app needs code, libraries, and dependencies so you can run it anywhere without a hitch.

## The 3 main components of Docker Engine are:

1. **Docker Daemon (dockerd)**:  
    The Docker Daemon (**dockerd**) is the core component that runs on the host system. It listens for Docker API requests and manages Docker objects (containers, images, networks). It’s the engine doing the heavy lifting behind the scenes.
    
2. **Docker CLI**:  
    The Docker Command-Line Interface (CLI) is the user-facing tool. You use it to run Docker commands like **docker run**, **docker build**, etc. The CLI communicates with the Docker Daemon via the Docker API to manage containers and images.
    
3. **Docker REST API**:  
    This is the interface that allows communication between the Docker CLI (or other external tools) and the Docker Daemon. The CLI uses this API to send commands to the daemon, making the Docker system easier to control and integrate with other tools.
    

So, while **Docker Daemon** (**dockerd**) and **Docker CLI** are indeed two components, the third one is the **REST API**, which links them together!

## The Problem Before Docker

Before Docker, developers relied on traditional virtualization using (VMs), which had several challenges:

* **Resource Heavy**: Each VM needed its own operating system, much like every tenant in a neighborhood owning a full house and garden—wasting resources.
    
* **Slow Startup**: Booting up a VM took time, similar to a new tenant moving into a house and having to set everything up from scratch. It was a lengthy process!
    

### Example: Virtualization

Virtualization is like dividing a house into rooms (virtual machines) that share the same utilities (hardware). The challenges are:

1. **Performance Overhead**: More rooms mean more competition for resources, like multiple people using the same water line, causing slowdowns.
    
2. **Resource Allocation**: It's like dividing one pizza (server resources). If not balanced, some rooms (VMs) get too much or too little.
    
3. **Security**: If one room has a leak (security issue), it can affect others. Ensuring isolation is critical.
    

## Resolution After Docker Came

"Instead of dividing a whole house into separate rooms (virtual machines), Docker is like giving each person (application) their own fully equipped apartment (container). Each apartment has what it needs (code, libraries) without the extra overhead of sharing the entire house (hardware).

### Solutions Docker offers:

1. **Efficiency**: Containers share the same operating system, like all apartments using the same building infrastructure. This makes them faster and lighter compared to VMs.
    
2. **Resource Management**: Each container uses only what it needs, like a person using just their own kitchen instead of a shared one. This avoids resource conflicts.
    
3. **Security**: Containers are isolated like separate apartments, so if one has an issue, it doesn’t affect others.
    

## Virtualization Before Docker: The Comparison (Most Asked Interview Question)

### Virtualization:

* **Full OS per VM**: Each VM has a complete operating system.
    
* **Resource Usage**: Heavy on resources due to multiple full OS instances.
    
* **Slower Startup Time**: Each VM takes time to boot up.
    
* **Management**: Handled by hypervisors like VMware or Hyper-V.
    

### Docker:

* **Shared OS Kernel**: Containers share the host OS, making them more efficient.
    
* **Lightweight**: Only the application and dependencies are included.
    
* **Fast Startup**: Containers start up almost instantly.
    
* **Managed by Docker Daemon**: All operations are handled through the Docker Daemon.
    

## Containerd and Dockerd: Understanding Their Roles

In addition to Docker, there are a couple of important components to know about:

* **containerd**: This is a core container runtime responsible for managing the lifecycle of containers. It pulls images, starts and stops containers, and handles storage but operates beneath Docker.
    
* **dockerd**: This is the Docker Daemon, sitting on top of containerd, interacting with the Docker CLI and REST API. It manages the entire Docker environment, ensuring everything runs smoothly.