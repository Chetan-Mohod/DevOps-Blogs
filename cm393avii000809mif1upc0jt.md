---
title: "Understanding Docker Multistage Builds: Intermediate Stages and Their Management"
datePublished: Fri Nov 08 2024 18:48:18 GMT+0000 (Coordinated Universal Time)
cuid: cm393avii000809mif1upc0jt
slug: understanding-docker-multistage-builds-intermediate-stages-and-their-management
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1731085514446/dbe91e6f-e40a-45d5-a675-da59f99852f5.png
tags: docker, multistage-docker-build, multi-stage-builds, docker-multistage-build, intermediate-build, final-stage, intermediate-stage

---

Docker multistage builds are an excellent way to create optimized, smaller images by separating the build process from the final runtime environment. However, when working with multistage builds, questions often arise about the management and handling of **intermediate stages**.

In this post, we’ll answer two common questions about multistage builds:

1. **What happens to the intermediate stages after the build process? Are they discarded or kept?**
    
2. **How can you use intermediate stages effectively during the build process?**
    

### **What Happens to Intermediate Stages After the Build?**

In a multistage build, each `FROM` statement defines a new stage in the process. Docker will create all these stages during the build, but it only retains the final image. The intermediate images those created in earlier stages are discarded unless explicitly referenced or saved.

Let’s break down how this works:

#### Example Dockerfile:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731084811358/a150db53-06e5-48cc-a5fb-75d78bc3987f.png align="center")

1. **First Stage (***build-stage*):
    
    * Here, you might use a larger base image (e.g., *python:3.9*) because it includes all the necessary build dependencies (compilers, libraries, etc.).
        
    * This image is used only to install dependencies and compile or build your application.
        
2. **Second Stage (***final*):
    
    * In the second stage, you use a smaller base image (e.g., *python:3.9*\-*slim*) which only contains the runtime environment for your application no extra build tools.
        
    * Docker copies the necessary files from the first stage to the second stage, ensuring that the final image is minimal and efficient.
        

**What happens to the first stage (***build-stage*) image after the build?  
Docker **discards** the *build-stage* image by default, keeping only the `final` image that is referenced last in the **Dockerfile**. This helps ensure that your final image is as small as possible.

### **How to Use Intermediate Stages in Docker Multistage Builds?**

While Docker discards intermediate stages after the build process completes, there are cases when you might want to **retain or test an intermediate stage**. Fortunately, Docker provides a way to do this.

#### 1\. **Using the** *\--target* Option to Build a Specific Stage:

The *\--target* flag allows you to **build only up to a specific stage** and stop there. This can be helpful if you want to test, debug, or save intermediate results before the build process is fully completed.

For example, let’s say you want to stop the build after the `build-stage` and save that image for testing purposes:

**docker build --target build-stage -t build-image .**

* This command tells Docker to build the image up to the `build-stage` and then stop.
    
* The result is an image containing all the dependencies and tools from the first stage, without moving to the second (slimmed down) stage.
    

#### 2\. **Manually Saving Intermediate Stages**:

Another approach to saving an intermediate stage is to **explicitly tag it** during the build process. You can do this using the `-t` flag to give the intermediate image a name.

Example: **docker build --target build-stage -t my-build-stage-image .**

This will:

* Build the *build-stage* as defined in the Dockerfile.
    
* Save this intermediate image as *my-build-stage-image*, which you can later inspect, push to a registry, or reuse in other Dockerfiles.
    

### **Why Use Intermediate Stages?**

Intermediate stages are a powerful feature because they allow you to **separate your build and runtime environments**, optimizing both.

* **Build Environment:** The first stage can contain everything needed for compiling, testing, or installing dependencies. Since it’s not part of the final image, you don’t need to worry about the size of this stage affecting your production image.
    
* **Runtime Environment:** The second stage uses a minimal base image (like *python:3.9-slim*) and only includes the necessary files and binaries. This ensures that your final image is smaller and more efficient.
    
* **Debugging and Caching:** By building only up to a specific stage, you can inspect your environment during development, test builds in isolation, or speed up builds by leveraging Docker’s layer caching system.
    

### **Conclusion**

Docker multistage builds are a powerful way to create optimized, efficient images by keeping only the necessary components in the final runtime image. Intermediate stages play a crucial role in separating the build and runtime processes, but they are discarded after the build unless you explicitly save or target them.

By using the *\--target* option or tagging intermediate images, you can test, save, and reuse intermediate stages for debugging or re-building purposes. This flexibility helps make your Docker builds faster, more efficient, and easier to manage.