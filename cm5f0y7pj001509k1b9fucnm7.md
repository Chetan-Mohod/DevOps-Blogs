---
title: "4. Scaling and Scheduling Pods in Kubernetes: All About Taints & Tolerance"
datePublished: Thu Jan 02 2025 07:48:30 GMT+0000 (Coordinated Universal Time)
cuid: cm5f0y7pj001509k1b9fucnm7
slug: 4-scaling-and-scheduling-pods-in-kubernetes-all-about-taints-tolerance
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735803866938/fefeede4-4f36-4af8-8aa0-6f5411fa31b8.png
tags: kubernetes, devops, cluster, devops-articles, kubernetes-container, devops-journey, 90daysofdevops, pods, 90daysofdevops-chanllenge, 90daysofdevops-devops-projectdevelopment-nonitbackground-github-docker-cloudplatforms-ec2-aws-elasticbeanstalk-lambdafunctions-devopspipelines-terraform-jenkins-docker-devsecops-scm-git-gitlab-bitbucket-buildtools-griddle-maven-ant-msbuild-monitoringtools-prometheus-grafana-ansible-ai-chatgpt-valueaddition-realworldproblems, 90daysofdevopschallenge, devopscommunity, taints, taints-and-toleration

---

> It is recommended to read this [blog](https://hashnode.com/post/cm5e0jnhn003709ma70tw3aqy) before continuing with this one.

In Kubernetes, **taints and tolerations** are like a "Do Not Disturb" sign and a "VIP Pass" for pods and nodes. Here's a simple way to understand:

---

### What are Taints?

Taints are labels applied to **nodes** to say:  
"Hey, only specific pods can run here. Others, stay away!"

### What are Tolerations?

Tolerations are added to **pods** to say:  
"Hey, I have permission to run on nodes with this 'Do Not Disturb' sign."

---

### Example:

Imagine a mall with a **VIP lounge** (node). The lounge has a sign:  
"Only VIP members allowed" (*Taint: key=vip, effect=NoSchedule*).

Now, a pod (customer) wants to enter.

* Regular customers without the VIP pass (*no toleration*) are not allowed inside.
    
* VIP customers with the pass (*toleration: key=vip*) are allowed.
    

---

### Why Use Taints and Tolerations?

They help ensure:

* Critical workloads run on specific nodes (e.g., high-performance hardware).
    
* Other workloads don’t overload sensitive nodes.
    

---

**<mark>Taints &amp; Tolerations:</mark> You can add a taint to a node to stop pods from being scheduled there unless they have a matching toleration. A taint puts a label on a node, showing that only certain pods with the right toleration can be scheduled on that node.**

***<mark>Note:</mark> The control plane is tainted by default, which is why pods are not created on the control plane.***

---

Lets understand it with an example:

### Taints Concept:

1. Create a `pod.yml` and apply the changes:
    

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
```

```bash
kubectl apply -f pod.yml
```

The pod is created and functioning properly because, by default, all the worker nodes are not tainted, allowing it to run there. Meanwhile, the control plane is tainted, which is why no pods are running on it.

2. Lets delete our `pod.yml`
    

```bash
kubectl delete -f pod.yml
```

3. List the nodes and taint one of them with mentioned command:
    

```bash
kubectl get nodes
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735801541349/68fd505a-7fae-4467-91cf-a3c10a608f9e.png align="center")

```bash
kubectl taint node acrobat-cluster-worker prod=true:NoSchedule
kubectl taint node acrobat-cluster-worker2 prod=true:NoSchedule
```

* `kubectl taint`:  
    This is the command used to **apply a taint** to a node in Kubernetes.
    
* `node`:  
    Specifies that the taint will be applied to a **node**.
    
* `acrobat-cluster-worker`:  
    The **name of the node** to which the taint is being applied. In this case, it’s `acrobat-cluster-worker`.
    
* `prod=true`:  
    This is the **taint key-value pair**. `prod` is the key, and `true` is the value. It’s used to label the node with this specific taint.
    
* `NoSchedule`:  
    The **effect** of the taint. It means that **no pods** will be scheduled on this node unless they have a matching **toleration** for `prod=true`.
    

Now, you might be wondering, since our control plane is already tainted and we have also tainted the remaining worker nodes, where will our pod run when we apply it? Let's find out.

```bash
kubectl apply -f pod.yml
```

You will observe that the pod remains in a pending status.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735801725477/503db446-e8d6-47f4-a943-f34c23531556.png align="left")

Let describe the pod and see the result:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735801949457/913e83b9-3007-4753-8d36-b4a5caee4e66.png align="center")

4. To remove the taint, you can use the following command. The command for untainting is similar to the taint command, with the only difference being the `-` at the end.
    

```bash
kubectl taint node acrobat-cluster-worker2 prod=true:NoSchedule-
```

Now you can verify that the pod is running by using the command `kubectl get pods`:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735802165330/528d26e7-6bb1-4896-9be1-7ecbf20445ba.png align="left")

To remove the taint from worker node1, use the following command:

```bash
kubectl taint node acrobat-cluster-worker prod=true:NoSchedule-
```

### Tolerance Concept:

1. To begin, open the `pod.yml` file:
    

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
  tolerations:
    - key: "prod"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
```

1. `tolerations:`  
    This section defines the **tolerations** for a pod. Tolerations allow the pod to **accept** taints applied to nodes.
    
2. `- key: "prod"`  
    The **key** of the taint this pod will tolerate. In this case, it matches the `prod` key from the taint applied to the node.
    
3. `operator: "Equal"`  
    The **operator** specifies how the value is compared. `Equal` means the pod will only tolerate the taint if the key-value pair exactly matches (in this case, `prod=true`).
    
4. `value: "true"`  
    The **value** for the key `prod`. The pod tolerates the taint if the value is `true`, matching the taint applied to the node.
    
5. `effect: "NoSchedule"`  
    The **effect** specifies what happens when the taint is applied. In this case, the effect `NoSchedule` allows the pod to be scheduled on the node, as long as it tolerates the `prod=true` taint.
    

### What it does:

This toleration ensures that the pod can be scheduled on a node with the `prod=true:NoSchedule` taint. Without this toleration, the pod would not be able to run on the node.

2. Now delete our pod which was running:
    

```bash
kubectl delete -f pod.yml
```

3. To apply a taint to our worker node1, use the following command to set the taint as prod=true:NoSchedule:
    

```bash
kubectl taint node acrobat-cluster-worker prod=true:NoSchedule
```

And apply a taint to our worker node2 using the following command to set the taint as dev=true:NoSchedule:

```bash
kubectl taint node acrobat-cluster-worker2 dev=true:NoSchedule
```

4. Now our pod is tainted for both the nodes, let see if it will work or not when we apply the changes:
    

```bash
kubectl apply -f pod.yml

kubectl get pods
```

You can see its running:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735803368167/6588e1f2-afa0-493f-b101-7d239b9fc37a.png align="center")

**WHY?** Because the pod can only run on `acrobat-cluster-worker` because it accepts the `prod=true` tag. It can't run on `acrobat-cluster-worker2` because that node has a different tag (`dev=true`) that the pod doesn't accept.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735803466535/17b29eb7-3107-4e82-9e8a-cd27e0017ccf.png align="center")

This is an explanation of the concept of Taints and Tolerations.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).