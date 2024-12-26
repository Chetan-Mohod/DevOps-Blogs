---
title: "Mastering Kubernetes: From Namespaces and POD Creation to Deployments, Auto-Healing, Rolling Updates, ReplicaSets, and StatefulSets"
datePublished: Fri Nov 22 2024 20:11:51 GMT+0000 (Coordinated Universal Time)
cuid: cm3t6g8rs000409l54vii6yov
slug: mastering-kubernetes-from-namespaces-and-pod-creation-to-deployments-auto-healing-rolling-updates-replicasets-and-statefulsets
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1732307641737/6d9af466-5f6d-43c4-9d48-cbe0f22c6dda.webp
tags: deployment, kubernetes, k8s, kubernetes-container, replicaset, kubernetes-architecture, namespaces, pods, kubernetes-pods, statefulsets, deploymentmanagement-replicaset-rollingupdates-rollback-kubernetes-scaling-deploymenterrors-applicationconfiguration-resourcemanagement-imagepullerror-insufficientpermission-limitranges-quota, rolling-update-kubernetes

---

* **Namespaces**
    
* **PODs**
    
* **Deployments**
    
* **ReplicaSets**
    
* **StatefulSets**
    

> Please read this blog before continuing with this one: [Click here](https://hashnode.com/preview/673e3c9b9f2d8fa3134b72f6)

**Steps to Setup Kind Clusters Using a Manifest File:**

1. First we need to create a cluster using the manifest file (config.yml). Refer file from previous blog.
    
2. Execute below command to create a cluster:
    

```bash
kind create cluster --name=chetan-cluster --config=config.yml
```

3. Now we will check with Kubernetes (API server → Controller Manager) to see if our Nodes are ready.
    

```bash
kubectl get nodes
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732213025450/0fe3b059-0dab-4404-b796-e9d48de056c3.png align="center")

Now the containers should be created. Check by running the `docker ps` command.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732213096961/997bded8-4586-43ab-a420-6c9cc5d63988.png align="center")

Three containers are running. We have created a cluster with one master node and the other nodes as workers.

This is how Kind clusters are set up.

---

### Namespaces:

* The master node assigns tasks to Worker Node1 to create an Nginx container, asks Node2 to create a MySQL container, and instructs Node3 to create another Nginx server. With two similar Nginx workers, there could be confusion. To avoid this, it's best to create groups or isolated environments, such as separate namespaces for Nginx, MySQL, etc.
    
* A namespace essentially acts as a group or isolation of resources.
    
* For instance, we can create a namespace called Production to run only production applications.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732213691202/21a00401-dbab-4086-b943-8c091765321b.png align="center")

### **How to Create a Namespace?**

You can refer to the official Kubernetes documentation: [Click Here](https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/).

For anything you want to learn or code, just search for Kubernetes followed by the "Concept\_name".

*Example: Kubernetes Namespace Yaml*

Steps to Create a Namespace:

1. Connect to your EC2 instance and navigate to the `k8s-practice` directory:
    

```bash
mkdir nginx        #create a directory for nginx where we create all the resource for it

cd nginx           #go to nginx

vim namespace.yml  #Mainfest File(What ever you want/desire you just need to write here)
```

```yaml
#Add below code into the mainfest file
kind: Namespace  #Type
apiVersion: v1   #When we provide this file to the API server using kubectl, the version is specified here.
metadata:        #Namespace Information
  name: nginx    #Namespace Name
```

2. Now, use `kubectl` to apply these changes.
    

```bash
kubectl apply -f namespace.yml
```

Validate:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732214744892/349f8c7c-9db8-4204-ae7e-d1fcc64e5fba.png align="center")

3. How to delete Namespace:
    

```bash
kubectl delete -f namespace.yml
```

---

### How to create POD?

Steps to Create a Pod:

1. Create Manifest file for Pod `vim pod.yml` :
    

```yaml
kind: Pod             #Type
apiVersion: v1
metadata:
    name: nginx-pod   #POD name
    namespace: nginx  #Under which namespace
spec:                 #Specifications of POD
    containers:       #Inside POD's container will run
        - name: nginx         #Container name   
          image: nginx:1.14.2       #Image will come from DockerHub
          ports:
            - containerPort: 80    #Generally Nginx application runs on Port 80
```

2. Always perform a dry-run test before executing:
    
    It won't apply the changes; it's just a simulation that generates the output.
    

```bash
kubectl apply -f pod.yml --dry-run=client
```

3. We will use kubectl to verify with the Controller Manager whether the pods for the nginx namespace have been created.
    

```bash
kubectl get pods -n nginx
```

It will display output like this because we only performed a dry-run test.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732216751568/43891022-b474-4a29-bda8-c917af5dd080.png align="left")

4. Run the command below to create the pods:
    

```bash
kubectl apply -f pod.yml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732216834842/019fb082-d043-45aa-8a9e-1753d6f1c59b.png align="left")

This is how you can create Pods.

---

### What is Deployment?

* In Kubernetes, a **Deployment** is like a manager for your app: it keeps the right number of instances running, handles updates without downtime, and fixes any issues automatically. Just set your app’s desired state, and Kubernetes does the rest!
    
* Whatever you need, you can specify in the deployment file. For example, if you want secret variables, environment variables, or 10 pods, you can include them in the deployment.
    

### **Why Do we need Deployment?**

* The reason we use Deployment is that a POD, which is the smallest unit, has a container running inside it.
    
* Example: If you want to scale the deployment due to high traffic, you shouldn't rely on just one POD. You should have multiple PODs so your application can balance the load properly. In this case, you need to set the desired state, which is why we use Deployment.
    

### **Why do we use it?**

* We use Deployment because we want our pod to function in a specific manner. We aim for our pod to be highly available and capable of automatic healing. This is important because it aligns with our desired state for the application.
    

Steps to implement a deployment:

1. Create `deployment.yml` file:
    
2. Add below code:
    

```yaml
kind: Deployment
apiVersion: apps/v1        #API version only for deployment
metadata:
  name: nginx-deployment
  namespace: nginx
  labels:           #key: values pairs       
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:            #template code is same like POD #Desired state applies to POD # Deployment applies to POD
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.14.2 
        ports:
        - containerPort: 80
```

* **apiVersion**:
    
    * For deployments, the `apiVersion` should be `apps/v1` because a Deployment's label selector is immutable after it is created.
        
    * The Deployment's job is to match labels with the node labels. If we want to create a node for MySQL, it will use the node with the MySQL label.
        
    * If you add labels and selectors in deployment.yml, you can't change them because they are immutable.
        
    * However, in other YAML files, they are mutable, and you can change them.
        

**<mark>Note: The concept of Labels and Selectors is often asked in interviews. I’ll cover this concept in upcoming blog.</mark>**

* **labels and selectors:** Labels in Kubernetes categorize resources with key-value pairs, while selectors filter and manage these resources based on their labels for efficient orchestration.
    
* **template: In template you add POD definition(code).** In Kubernetes, a deployment template defines the configuration for pods, specifying how they should be created and managed across replicas.
    

3. Now delete the nginx pod we created earlier because we're going to create a new one.
    

```yaml
kubectl delete -f pod.yml
```

4. To apply this deployment to your Kubernetes cluster, execute the following command:
    

```yaml
kubectl apply -f deployment.yml --dry-run=client  # First perform dry run

kubectl apply -f deployment.yml  #To apply deployment on cluster
```

5. Now, let's validate the deployment:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732270674977/8c5ff7cd-59ca-4582-b8ce-308a14d9f296.png align="center")

Now, we have 3 NGINX pods running:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732270712697/1a470818-820c-43a6-9262-63b94f7dacc9.png align="center")

---

### **Interesting Auto-Healing Concept:**

* If you delete the pod accidently, lets see what happens:
    

```yaml
#Delete the exisiting pods

kubectl delete pod pod1_name pod2_name -n nginx
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732271369246/daa19e00-d039-474f-a165-ccc25fd617e7.png align="center")

* Even if you delete the pods, they will automatically recover, and you can see the age of the new pods. This is called **Auto-Healing**.
    
* Your pod cannot be deleted, no matter how much traffic there is.
    

---

### **Deployment Scaling Replicas**

* To adjust the number of pod replicas, you typically need to modify the Manifest file (deployment.yml) each time. However, there is a more efficient method to achieve this. Let's explore it.
    

```yaml
kubectl scale deployment nginx-deployment -n nginx --replicas=5
```

You can observe the results of the scaling operation.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732271733297/7206dd35-dc2b-42ea-8d4e-bd7358b4d2b1.png align="center")

* If you modify the `deployment.yml` file to set the replicas to 3, these changes will be applied because it reflects our most recent configuration preference.
    
* You can increase the number of replicas using both methods.
    

---

### **Rolling Updates:**

* Run `kubectl get pods -n nginx -w` to continuously monitor the currently running pods, updating every 2 seconds.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732275452331/a7438a4b-a2f0-499e-8a5f-26ee135ad1e8.png align="center")

* Now we can open another tab and change the nginx version.
    

```yaml
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.16.1 -n nginx
```

* Now you can go back to the previous tab.
    
* You will notice that it does not terminate and create all the pods simultaneously. Instead, the pods do not go down all at once; the process follows a **Rolling Update** strategy.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732275736536/865ef946-239f-4b7b-a59b-42647eb814aa.png align="center")

### How to enter into a Container?

1. List the pods for nginx.
    

```yaml
kubectl get pods -n nginx  #It will print pod_name
```

2. Command to enter into a Container in Kubernetes:
    

```yaml
kubectl exec --stdin --tty "replace_pod_name" -n nginx -- /bin/bash
```

`--stdin --tty`: These flags allow you to interact with the container. `--stdin` keeps the input open, and `--tty` creates a terminal like interface, making it feel like you’re working directly inside the container.

---

### **ReplicaSets:**

* ReplicaSets represent the desired state of having a specific number of replicas. The ability of ReplicaSets to create replicas from a template is utilized by a deployment. As a result, ReplicaSets are not commonly used on their own anymore. Instead, replicas are managed directly within the deployment.
    
* **<mark>A deployment manages ReplicaSets automatically</mark>**
    

---

### StatefulSets:

* Lets take an example:
    
    * We have 3 MySQL pods, and we want to reduce the number to 2 using a Replicas. This means we need to delete 1 pod. The question arises: **what happens to its data?**
        
    * So, whenever a pod is deleted, its state should be assigned to another pod.
        
    * StatefulSets function similarly to deployments and replicas, but they maintain a state. This ensures that whenever your pods are deleted, their state is preserved.
        
    * Its basically ReplicaSets that maintain state.
        
* This is just overview of StatefulSets, I’ll cover the hands-on in our next blog.
    

---

#K8s Tips & Tricks Click Here → [Kubectl tips and trick](https://discuss.kubernetes.io/t/kubectl-tips-and-tricks/192/4)

---

### Interview Question for Experienced DevOps Engineer:

1. How do you update your deployment, What strategies you use to updating your deployment?
    
    * We updated via Rolling updates.
        
2. What is Watch?
    
    * `kubectl get pods -n nginx -w`
        
    * In Kubernetes, **Watch** lets you keep an eye on resources in real time. Instead of having to keep checking for updates, Watch shows you any changes as soon as they happen. For example, if you’re watching Pods, you’ll see right away when a Pod is added, updated, or deleted. It’s super helpful for tracking things like deployments or catching issues as they come up.