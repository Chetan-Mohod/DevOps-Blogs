---
title: "Kubernetes Services: Understanding the Essentials + Deploy a Node.js App in Just 3 Minutes!"
datePublished: Sun Nov 24 2024 15:07:40 GMT+0000 (Coordinated Universal Time)
cuid: cm3vqgr85001u08l17wqb2nbg
slug: kubernetes-services-understanding-the-essentials-deploy-a-nodejs-app-in-just-3-minutes
tags: kubernetes, services, kubernetes-container, kubernetes-services

---

> It is recommended to read this blog first: [Click here](https://hashnode.com/draft/673f695504bc25b5d81037b7) before proceeding with this one, to avoid feeling overwhelmed.

### What is Service?

* In a Kubernetes setup, we have three nodes: one Master and two Worker nodes, as illustrated in the diagram. When a user wants to access an nginx instance, they might wonder which specific instance they will connect to, given that there are three pods, each running nginx. The user always interacts with a Service proxy.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732292400102/a7d268c0-2e68-4d7c-9f1b-4734d63ad8c4.jpeg align="center")
    
* <mark>A Service is a resource that allows you to map the port number on your node to the port number on a container in your deployment.</mark>
    
* A Service takes a port from an available pod and maps it through the service proxy, directing the user to that available pod.
    

**Service Types: There are four types of services:**

1. **LoadBalancer**: Typically used in cloud environments. Imagine a Load Balancer (LB) as a security guard at your office entrance. When you ask to meet someone (a pod named XYZ), the guard might say XYZ is not available because the LB doesn't know XYZ personally. This is why you need to map the deployment and service first.
    
2. **NodePort**: Generally used for local environments.
    
3. **Cluster IP**: The default type, used for internal communication within the cluster.
    
4. **ExternalName**: Maps a service to a DNS name.
    

**<mark>If you don’t mention any service type, then it is called headless service.</mark>**

### How to create a service?

1. To create a service, you need to start by creating a manifest file named `service.yml`.
    

```yaml
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
  namespace: nginx

spec:
  selector:             
    app: nginx
  ports:
    - protocol: TCP
      targetPort: 80     #It is POD port  (Port inside from POD ) (Service)
      port: 80           #Port which we want to map (Port in Service) (Host)
  type: NodePort         #Service Type (Because we're performing it on Local)
```

Why don't we use labels with selectors?

1. This is because in `deployment.yml`, we have already included `matchLabels`
    

2. Now execute this `service.yml`
    

```yaml
kubectl apply -f service.yml
```

3. List the services for our nginx
    

```yaml
kubectl get service -n nginx
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732295617814/28e64d18-2343-406f-b80c-7ea5dd8cbd76.png align="center")

Why is our port 80 mapped to 31898?

* When you want to get the `nodePort` of a particular node, it falls within the range of 30000 to 32000. If you're running a cluster in Docker, it's fine not to specify a port, as it will automatically select a port from that range and map it to our port.
    
* If desired, you can specify the `nodePort` in the `service.yml` file as the third port and assign a value within the range of 30000 to 32000.
    

```yaml
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
  namespace: nginx

spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      targetPort: 80 #It is POD port  (Port inside from POD )
      port: 80  #Port which we want to map (Port in Service)
      nodePort: 30001
  type: NodePort
```

* Now configure the file again and see the results:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732295991314/1d5ca652-5f1c-4504-9430-7d866a4985c8.png align="center")

* Now if you want to go to the port on localhost, it won’t work because that port is running inside Docker, so it will work inside Docker.
    
* Now we need to map a Docker container with an EC2 instance. We will use port-forwarding.
    

**Port-Forwarding:**

```bash
kubectl port-forward service/nginx-service -n nginx 80:80 --address=0.0.0.0 &
```

* If the above command does not forward our port, it may be due to a system permission issue. Use the following command to address system permissions:
    

```bash
 sudo sysctl -w net.ipv4.ip_unprivileged_port_start=0
```

* This command will disable all ports set to 0. Port 80 is considered privileged, which is why it does not allow exposure by default. Since our EC2 instance is hosted on AWS, AWS restricts the mapping of privileged ports.
    

The output should appear as follows:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732300279059/9ddffd74-0708-4e58-b613-6f1660c10a4d.png align="center")

* Now you can take the public ip from your instance:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732300361045/488cf9a6-d32a-4361-b793-dfaea77e80c6.png align="center")

* And paste it into the browser : http://public\_ip:80 and then below window will appear.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732300417766/e8b81f2a-8496-49d8-b016-ed6802fd4523.png align="center")

### Deploy a Node.js app in just 3 minutes.

Steps

1. Create a folder and copy the nginx directory into it:
    

```bash
mkdir nodejs && cp -r nginx/*.* nodejs
cd nodejs
```

2. Now we are creating a Node.js app, so update the metadata in all the YAML files.
    

```yaml
# vim namespace.yml
kind: Namespace
apiVersion: v1
metadata:
  name: nodejs
```

```yaml
#vim pod.yml
kind: Pod
apiVersion: v1
metadata:
  name: nodejs-pod
  namespace: nodejs
  labels:
    app: nodejs
spec: #Spec inside the POD
  containers:
    - name: nodejs-container
      image: chetan0103/node-app:latest  #will be come from DockerHub
      ports:
        - containerPort: 8000
```

```yaml
#vim deployment.yml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: nodejs-deployment
  namespace: nodejs
  labels:
    app: nodejs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodejs
  template:
    metadata:
      labels:
        app: nodejs

    spec:
      containers:
      - name: nodejs-container
        image: nodejs
        ports:
        - containerPort: 8000
```

```yaml
#vim service.yml
kind: Service
apiVersion: v1
metadata:
  name: nodejs-service
  namespace: nodejs

spec:
  selector:
    app: nodejs
  ports:
    - protocol: TCP
      targetPort: 8000
      port: 8000
  type: NodePort
```

3. Now execute all the components:
    

```bash
kubectl apply -f .

#Or use below command, because with deployment we can create a pod

kubectl apply -f namespace.yml -f deployment.yml -f service.yml
```

4. To proceed, let's execute the port-forward command.
    

```bash
kubectl port-forward service/nodejs-service -n nodejs 8000:8000 --address=0.0.0.0
```

5. Access the EC2 instance and update the inbound rules to allow traffic on port 8000.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732303335689/75e8c129-5604-4b47-b9e3-1b32e467971c.png align="center")

6. Now, take the public IP of the EC2 instance and enter it in your browser, using port 8000 (http://public\_ip:8000).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731354381574/fd42cf7d-ac3b-46f3-bcb4-9d670a711500.png align="left")

Now you can deploy any application on Kubernetes by simply navigating to the deployment configuration and updating the image name. That's all it takes.

**Deploy my-not-app**

1. set Image:
    

```bash
kubectl set image deployment/nodejs-deployment nodejs-container=chetan0103/my-not-app:latest -n nodejs
```

2. run port forwarding command:
    

```bash
kubectl port-forward service/nodejs-service -n nodejs 8000:8000 --address=0.0.0.0
```

You can go to browser and see the results.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732304020334/0a1a95a3-34b8-4cb4-9ae2-0d90ae62c8af.png align="center")

However, it is advisable not to use the set image command, as it can occasionally disrupt the port connection.

It is recommended to always use `deployment.yml` to update the image.

### Happy Learning :)

Chetan Mohod