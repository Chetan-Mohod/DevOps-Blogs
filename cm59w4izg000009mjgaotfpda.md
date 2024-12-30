---
title: "1. Scaling and Scheduling Pods in Kubernetes: All About HPA"
datePublished: Sun Dec 29 2024 17:34:36 GMT+0000 (Coordinated Universal Time)
cuid: cm59w4izg000009mjgaotfpda
slug: 1-scaling-and-scheduling-pods-in-kubernetes-all-about-hpa
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735493623091/2f4ea61f-5248-43eb-9b25-e38fa59781ed.png
tags: kubernetes, devops, k8s, horizontal-scaling, 90daysofdevops, 90daysofdevops-chanllenge, hpa, 90daysofdevopschallenge, horizontal-pod-autoscaler

---

# Horizontal Pod Auto-Scaler (HPA)

As this is my 50th blog, here’s a detailed exploration of Kubernetes Horizontal Pod Autoscaler (HPA) and how it simplifies scaling and scheduling.

Steps to utilize the Horizontal Pod Autoscaler (HPA) for pods in Kubernetes:

1. Set up a server on any cloud provider. In this example, AWS is used, and an EC2 instance has been created with `t2.medium` instance. If you are unfamiliar with AWS, you can refer to any of my previous blogs for guidance on creating an EC2 instance. And connect with instance via SSH.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735465077514/7f3270e3-5771-4aa4-b057-5a3f883cc0a2.png align="center")

2. Now fork this repository: [kubestarter](https://github.com/Chetan-Mohod/kubestarter) and go to this repo and pull the latest code.
    

Commands:

```bash
git clone https://github.com/Chetan-Mohod/kubestarter.git

cd kubestarter

git pull
```

3. Now go to the kind-cluster and create a new cluster with three nodes using the `config.yml` file that we used in our previous blogs.
    

```bash
cd kind-cluster

kind create cluster --name=acrobat-cluster --config=config.yml
```

Concept that demonstrate about HPA:

* Assume you have a cluster with a `t2.medium` instance that includes three nodes: one master and two worker nodes. When you run a pod on worker node1, the pod automatically checks the resources and limits of that node. Resources refer to the amount of RAM needed to run the pod, while limits indicate the maximum resources the pod can use before it cannot handle more traffic.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735467535119/90efc719-b46d-4519-b6e5-3ecf70523c8a.png align="center")

* For example, if your pod uses 1 GB of RAM with a limit of 512 MB, it means that once the pod reaches 512 MB of RAM usage, it cannot handle additional traffic. To address this, we need to scale the pod so that it automatically creates another pod to share the load. This process is known as Auto-Scaling.
    
    * Auto-Scaling relies on metrics. Kubernetes introduces a Metrics Server, which checks the resource usage of our pods and uses this information to distribute the load. Metrics include storage, RAM, network, traffic, and load, and based on these, it automatically creates additional pods.
        
    * Let's automate this process.
        

4. Run the command below to check if Metrics are available:
    

```bash
kubectl top node
```

If it shows this message, it means our cluster is not ready for HPA.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735467371575/6daeab24-c35b-4c61-8a81-66cb72b4858c.png align="left")

WHY it is not ready?

* To perform HPA, we need the Metrics API, which provides information about your cluster resources like CPU and storage.
    

HOW to install Metrics Server?

* You can refer our repository: [Click here](https://github.com/Chetan-Mohod/kubestarter/tree/main/HPA_VPA) Or follow below commands:
    
* If you’re using Kind cluster install Metrics Server:
    

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

* Edit the Metrics Server Deployment
    

```bash
kubectl -n kube-system edit deployment metrics-server
```

Our metrics server will be install in `kube-system` namespace. Above command will edit the deployment which we have downloaded.

Add this argument to the file because you are in a virtual machine and Docker container. There are several components like SSL, TLS, etc., within it, and these networking elements are not secure. This can cause issues in a cluster. Therefore, include the following commands to secure these elements.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735468418148/e70d7295-a5e1-41af-ba13-90a18676a67a.png align="center")

```yaml
- --kubelet-insecure-tls
- --kubelet-preferred-address-types=InternalIP,Hostname,ExternalIP
```

* Restart the deployment
    

```bash
kubectl -n kube-system rollout restart deployment metrics-server
```

* Verify if the metrics server is running
    

```bash
kubectl get pods -n kube-system
kubectl top nodes
```

You can see that it is running:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735468716356/dae73b9b-3d37-4bf0-956c-be970621e8fb.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735468757039/26e51f5a-8555-41c7-8b57-4030a8c7ad70.png align="left")

Now go back to the previous folder.

```bash
cd ..
```

5. Now, we aim to run an application here, apply load to it, and scale it accordingly.
    

There are two types of Scaling Horizontal & Vertical:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735470402544/f262d884-fbaf-432e-96e1-0b8d70e85af1.png align="center")

```bash
cd HPA_VPA

ls -lrt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735470948120/2e35a756-d534-4824-bb5e-d2927bc482dd.png align="left")

Apache is a server that displays a default webpage and is used to serve HTML files, configuration files, servers, and applications. It is also known as httpd.

Create a `apache-deployment.yml` file from scratch. You can refer to the Kubernetes.io official website by searching for [Kubernetes HPA Yaml](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/). You can see in the code I have added the comments for resources and limits. And just replace metadata, labels, and image name from containers.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  labels:
    app: apache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
      - name: apache
        image: httpd:2.4
        ports:
        - containerPort: 80
        resources:
          requests:    #minimum request to run the app
            cpu: 100m
            memory: 128Mi
          limits:      #Maximum limit is 200m after that pod will fail
            cpu: 200m
            memory: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: apache-service
  labels:
    app: apache
spec:
  selector:
    app: apache
  ports:
    - protocol: TCP
      port: 80
```

6. Now, apply the changes:
    

```bash
kubectl apply -f apache-deployment.yml

kubectl get pods
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735472684257/42368048-2e03-4a85-89be-7b29b3428db0.png align="left")

7. Port Forward our service
    

```bash
kubectl port-forward service/apache-service 80:80 --address=0.0.0.0 &
```

If you receive below error

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735472814854/65defdd6-d301-4be5-babd-64e0bed9e30f.png align="center")

Then run below command, `-E` in `sudo` keeps your settings (like `KUBECONFIG`) when running `kubectl` as superuser.

```bash
sudo -E kubectl port-forward service/apache-service 80:80 --address=0.0.0.0 &
```

8. Go to EC2 instance and copy the public ip and paste it into the browser.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735473094190/7c3f636d-2953-476e-9b47-73189dba59a5.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735473166441/4f8766d5-5fdd-4c38-b2df-70547cd2ed18.png align="left")

9. To obtain more detailed information about our pod, execute the following command:
    

```bash
kubectl get pods -owide

kubectl get pod pod_name
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735473271263/497fbb8a-c9cd-4a9c-b2bf-7cebd0e21c8e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735473651047/e50f7ff6-696f-4089-a2da-01918e55365a.png align="center")

## Auto Scaling using HPA:

10. We aim to enable our pods to automatically scale in response to increased load.
    
    **How can this be achieved?**
    
    * Remove existing `apache-hpa.yml` & `apache-vpa.yml` from HPA\_VPA directory.
        
    * Create `apache-hpa.yml` from scratch, we will just create simple Apache Auto-Scaler.
        
        ```yaml
        #vim apache-hpa.yml
        
        apiVersion: autoscaling/v2
        kind: HorizontalPodAutoscaler
        metadata:
          name: apacha-hpa
        spec:
          scaleTargetRef: # HPA replica target to our deployment, that's why we add apiVersion, kind, name of deployment
            apiVersion: apps/v1
            kind: Deployment
            name: apache-deployment
        
          minReplicas: 1
          maxReplicas: 5
          metrics:
          - type: Resource
            resource:
              name: cpu
              target:
                type: Utilization
                averageUtilization: 5
        ```
        
        * Apply the changes:
            
        
        ```bash
        kubectl apply -f apache-hpa.yml
        
        kubectl get hpa
        ```
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735477244981/c4df1028-d2a9-4290-8d71-7329677c668e.png align="center")
        
        You can see that only one replica is currently running:
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735477772227/5964a0e7-2115-4e5b-8b46-4a6141d302f2.png align="left")
        
11. Next, we will create load on our server using "BusyBox", refer from GitHub README.
    

```bash
kubectl run -i --tty load-generator --image=busybox /bin/sh

while true; do wget -q -O- http://apache-service.default.svc.cluster.local; done
```

12. Now you can check the HPA, and you'll see it creates 5 replicas.
    

```bash
kubectl get hpa

kubectl get pods
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735478295390/2bd22b7e-1bc5-488c-a778-87c680726145.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735478333403/4645d6c2-bb34-4a47-9762-ae0eb34ee5bc.png align="left")

13. Now, delete the load-generator pod to stop generating additional load.
    

```bash
kubectl delete pod load-generator
```

Application will still works:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735478644435/ec76495c-e87c-4f53-9157-b2403e9bc8df.png align="left")

**This process shows how Horizontal Pod Auto-Scaling works. I’ll cover VPA in upcoming blog.**

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).