---
title: "Understanding the Difference Between Kind config.yml and Kubernetes kubeconfig"
datePublished: Tue Dec 24 2024 17:51:36 GMT+0000 (Coordinated Universal Time)
cuid: cm52rj4pi002409mhhsr8hv2a
slug: understanding-the-difference-between-kind-configyml-and-kubernetes-kubeconfig
tags: kubernetes, devops, k8s, 2articles1week, devops-articles, devops-journey, kubeconfig, devopscommunity, configyml

---

When you're working with Kubernetes, you'll come across two configuration files that can seem a bit confusing at first: **Kind** `config.yml` and **Kubernetes** `kubeconfig`. While both use YAML format and deal with cluster configurations, they serve entirely different purposes and are used in different contexts.

In this post, we’ll break down the differences between these two configuration files and explain how they fit into the Kubernetes ecosystem. Let’s dive in!

---

### **What is the Kind** `config.yml`?

If you’re familiar with **Kind** (Kubernetes in Docker), you know it’s a tool designed to help you create local Kubernetes clusters inside Docker containers. It's perfect for development and testing purposes. When you create a cluster with **Kind**, it uses a configuration file commonly called `config.yml` to define the cluster setup.

This file is your **blueprint** for the cluster. You can specify things like how many nodes you want in your cluster (e.g., a control plane and worker nodes), networking settings, and even things like port mappings between the Docker containers. Essentially, the Kind `config.yml` is your chance to tell Kind exactly how you want your local Kubernetes cluster to be structured before it even starts creating it.

#### **Example of a Kind** `config.yml`:

```bash
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30000
        hostPort: 80
        protocol: TCP
  - role: worker
  - role: worker
networking:
  podSubnet: "10.244.0.0/16"
  serviceSubnet: "10.96.0.0/12"
```

In this example:

* We have a **control-plane** node (the Kubernetes master) and two **worker** nodes.
    
* There's a networking configuration that specifies IP ranges for the Pods and Services in the cluster.
    

The Kind `config.yml` file is read when you run `kind create cluster`, and it helps **Kind** know exactly how to build your cluster.

---

### **What is the Kubernetes** `kubeconfig`?

Now, let’s talk about the **kubeconfig** file which is probably the file you’ve heard about the most when working with Kubernetes. The `kubeconfig` is like the **key** to accessing your Kubernetes cluster. Once your cluster is set up and running (whether it's a local Kind cluster or a large-scale cloud-based one), the kubeconfig file contains all the **access details** needed to connect to the cluster.

This includes important info such as the **API server URL**, your **authentication details** (e.g., tokens, certificates), and the **contexts** that allow you to switch between different clusters or namespaces with ease.

#### **Example of a Kubernetes** `kubeconfig`:

```bash
apiVersion: v1
kind: Config
clusters:
  - name: my-cluster
    cluster:
      server: https://127.0.0.1:6443
      certificate-authority-data: <BASE64_ENCODED_CERT>
users:
  - name: kind-user
    user:
      client-certificate-data: <BASE64_ENCODED_CERT>
      client-key-data: <BASE64_ENCODED_KEY>
contexts:
  - name: kind-my-cluster
    context:
      cluster: my-cluster
      user: kind-user
current-context: kind-my-cluster
```

In this example:

* The `server` field specifies the API server's URL that kubectl should connect to.
    
* The `certificate-authority-data` is used to authenticate the connection.
    
* The `contexts` section defines a cluster and user combination, making it easy to switch between different Kubernetes clusters or users.
    

The **kubeconfig** is crucial for interacting with your Kubernetes cluster using `kubectl` or any other Kubernetes management tool. It’s what allows you to authenticate and send commands to the cluster.

---

### **How They Work Together**

When you run the `kind create cluster --config=config.yml` command, Kind reads the `config.yml` to build the cluster. Once the cluster is up, Kind updates your **kubeconfig** file (typically located in `~/.kube/config`) with the necessary details to allow `kubectl` to interact with the cluster.

So, while the Kind `config.yml` is all about **setting up** the cluster, the kubeconfig is about **accessing** it after creation.

---

The `config.yml` is used during the creation of the cluster and helps you configure the cluster’s settings, whereas the `kubeconfig` is used to connect to the cluster and manage it once it’s up and running.

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).