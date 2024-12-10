---
title: "Understanding CMD vs ENTRYPOINT in a Dockerfile"
datePublished: Mon Nov 04 2024 19:47:44 GMT+0000 (Coordinated Universal Time)
cuid: cm33fnwes000308l8cm0t2che
slug: understanding-cmd-vs-entrypoint-in-a-dockerfile
tags: docker, devops, cmd, docker-images, entrypoint

---

When working with Docker, a common point of confusion arises between the **CMD** and **ENTRYPOINT** instructions in a Dockerfile. Both are used to specify what should run inside a container, but they behave differently and are suited to different use cases. In this blog post, we'll break down the differences between **CMD** and **ENTRYPOINT**, explore their use cases, and provide examples to clarify when to use one over the other.

---

### What are CMD and ENTRYPOINT?

In simple terms:

* **CMD** defines **default commands or arguments** that get executed when a container starts. However, it can be **overridden** if the user specifies a different command when running the container.
    
* **ENTRYPOINT** defines the **main command** that always runs inside the container. It is **not easily overridden**, though it can accept arguments if provided.
    

Both instructions are used in Dockerfiles to tell Docker what process to run inside the container when it starts, but the behavior differs in terms of flexibility and control.

---

### CMD Instruction

The **CMD** instruction provides **default values** for an executing container. You can think of it as a fallback mechanism if no command is specified when running the container, Docker will use whatever is defined by **CMD**.

* **Types of CMD**:
    
    * **Shell form**: **CMD \[""\]**
        
    * **Exec form**: **CMD \["", "", ""\]**
        

#### Example 1: Basic CMD in a Dockerfile

In this example, if you run the container without specifying any command: **docker run mycontainer** It will print: **Hello from CMD!**

However, if you run the container with a custom command: **docker run mycontainer echo "Overridden CMD"** It will override the **CMD** instruction and print: **Overridden CMD**

**Key Points:**

* **CMD** can be overridden.
    
* It's useful for setting default behavior that can be changed at runtime.
    

#### Example 2: CMD with a Shell Form

Here, Docker executes the command using the system’s default shell (**/bin/sh -c**). It works the same as typing it in the shell manually.

---

### ENTRYPOINT Instruction

**ENTRYPOINT** is used when you want to ensure that a specific command is **always executed**. Even if the user specifies arguments when running the container, **ENTRYPOINT** will always run as the primary command, and the user-provided arguments are passed to it.

* **Types of ENTRYPOINT**:
    
    * **Shell form**: **ENTRYPOINT**
        
    * **Exec form**: **ENTRYPOINT \["", "", ""\]**
        

#### Example 3: Basic ENTRYPOINT in a Dockerfile

If you run the container: **docker run mycontainer** It will print: **Hello from ENTRYPOINT!**

If you try to run it with a custom command like this: **docker run mycontainer "Overridden ENTRYPOINT"** It will **not** override the entrypoint. Instead, it will append the argument to the **ENTRYPOINT**: **Hello from ENTRYPOINT! Overridden ENTRYPOINT**

**Key Points:**

* **ENTRYPOINT** is not easily overridden.
    
* It ensures that a specific command is always executed.
    
* You can append arguments to the **ENTRYPOINT** when running the container.
    

#### Example 4: Combining ENTRYPOINT with CMD

A common pattern is to use both **ENTRYPOINT** and **CMD** together. In this setup, **ENTRYPOINT** defines the main command, and **CMD** defines the default arguments for the entrypoint. This allows for flexible configuration while ensuring the core behavior remains the same.

* If you run the container without specifying any command: **docker run mycontainer** It prints: **Hello from CMD!**
    
* If you run the container with additional arguments: **docker run mycontainer "Overridden CMD"** It prints: **Overridden CMD**
    

In this case, **ENTRYPOINT** is always `echo`, and **CMD** provides the default argument to it. If you provide your own argument, it replaces the **CMD** argument but not the **ENTRYPOINT**.

---

### Key Differences Between CMD and ENTRYPOINT

1. **Overriding Behavior**:
    
    * **CMD** can be overridden by providing a different command when running the container.
        
    * **ENTRYPOINT** is harder to override; only its arguments can be changed.
        
2. **Use Cases**:
    
    * Use **CMD** when you want to provide **default arguments** that can be easily overridden.
        
    * Use **ENTRYPOINT** when you need a **guaranteed command** to run, regardless of user input.
        
3. **Best Practice**:
    
    * Combining **ENTRYPOINT** and **CMD** gives you flexibility. The **ENTRYPOINT** is fixed, and the **CMD** can provide default arguments that can be overridden if needed.
        

---

### Practical Use Cases

#### Use Case 1: Web Server with CMD

Let’s say you are setting up a simple web server. You may want the server to run with default settings but allow users to override the defaults if needed.

This setup allows the container to start the Nginx web server with default settings. If the user wants to customize Nginx behavior, they can provide different commands when running the container.

#### Use Case 2: Database Container with ENTRYPOINT

For a database container, you might want to enforce that the database always starts but allow users to pass additional arguments for customization.

In this case, **postgres** will always be the command that runs, but users can provide additional flags or arguments, such as a different data directory, when starting the container.

---

### Conclusion

Understanding the differences between **CMD** and **ENTRYPOINT** is essential when designing Docker containers. While both serve the purpose of running commands, they offer varying levels of flexibility and control. **CMD** is best suited for setting default commands that users can easily override, whereas **ENTRYPOINT** is ideal when you need to enforce a specific command to run inside your container.

By combining both, you can build versatile and robust Docker images that balance default behavior with user configurability.