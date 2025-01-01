---
title: "3. Scaling and Scheduling Pods in Kubernetes: All About DaemonSets"
datePublished: Wed Jan 01 2025 14:49:25 GMT+0000 (Coordinated Universal Time)
cuid: cm5e0jnhn003709ma70tw3aqy
slug: 3-scaling-and-scheduling-pods-in-kubernetes-all-about-daemonsets
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735742770102/b800125d-42df-44bc-bc5b-f29c04a6f678.png
tags: scaling, kubernetes, automation, devops, devops-articles, devops-journey, 90daysofdevops, 90daysofdevops-chanllenge, 90daysofdevopschallenge, devopscommunity, daemon-sets

---

### What is a DaemonSet in Kubernetes?

Think of a **DaemonSet** as a dedicated delivery system that ensures every stop on a route gets exactly what it needs. In Kubernetes, it guarantees that a specific pod runs on every node in your cluster (or a subset of nodes, if you prefer).

---

### Let’s Break it Down with an Example:

Imagine you’re managing a pizza chain. Each branch (node) needs:

* A pizza oven (to make pizza).
    
* A cash register system (to handle payments).
    

Now, to ensure smooth operations:

1. You deploy one pizza oven and one cash register at every branch.
    
2. Anytime a new branch opens, it automatically gets these essentials.
    
3. If a branch closes, the pizza oven and cash register are removed.
    

In Kubernetes, the **pizza oven and cash register** are like the pods deployed by a DaemonSet. It ensures that all nodes (branches) have these tools running consistently.

---

### Real-Life Tech Analogy:

Imagine you’re running a monitoring or logging service (like Prometheus Node Exporter or Fluentd). You want every machine in your data center (node in your cluster) to:

1. Track system metrics like CPU usage or memory.
    
2. Send logs for analysis.
    

A **DaemonSet** ensures that these monitoring tools run on every machine, no matter how many machines are added or removed. It's your "set-it-and-forget-it" solution.

---

### Why is it Cool?

* **Automation**: New nodes automatically get the pods they need.
    
* **Efficiency**: You don’t have to manually deploy monitoring/logging services on each node.
    
* **Consistency**: All nodes are equipped with the same tools.
    

---

### Why DaemonSet is Beneficial:

1. **No Manual Placement**: You don’t need to say, "Place this pod on every node." DaemonSet handles it.
    
2. **Dynamic Scaling**: If a new node joins the cluster, DaemonSet automatically deploys the pod there. No extra config is required.
    
3. **Uniformity**: Ensures every node has the same service (like monitoring or logging), making it reliable for tasks that **must run on all nodes**.
    
4. **No Overlapping Pods**: Unlike ReplicaSets, which might run multiple pods on one node, DaemonSet ensures one pod per node for its task.
    

---

### Steps to use DaemonSets:

Please refer to this [blog](https://hashnode.com/post/cm59w4izg000009mjgaotfpda) for creating our clusters, and then follow the steps below:

1. Create `daemonset-deploy.yaml` file.
    

```yaml
apiVersion: apps/v1
kind:  DaemonSet
metadata:
  name: nginx-daemonset
  labels:
    app : nginx
spec:
  template:
    metadata:
      labels:
        app: nginx
      name: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      app: nginx
```

This `daemonset-deploy.yaml` file is similar to our `deployment.yml` file, doesn't it? The main difference is the kind type. By using DaemonSet as the kind type, it assigns each pod to an individual node.

2. Now apply the changes:
    

```yaml
kubectl apply -f daemonset-deploy.yaml
```

Although we have 2 worker nodes and the file specifies creating only one pod, running the command `kubectl get ds` will show 2 pods. This is because DaemonSet automatically assigns one pod to each node.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735742512015/d1de7cfd-68e9-4c30-9973-3222ee17059b.png align="center")

This is how a DaemonSet operates.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).