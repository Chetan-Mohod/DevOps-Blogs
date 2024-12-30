---
title: "2. Scaling and Scheduling Pods in Kubernetes: All About VPA"
datePublished: Mon Dec 30 2024 18:53:15 GMT+0000 (Coordinated Universal Time)
cuid: cm5bedium000509jz0gto6jm3
slug: 2-scaling-and-scheduling-pods-in-kubernetes-all-about-vpa
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735584723633/dbe93693-dd9c-46ea-bf63-fd8ba7e4b131.png
tags: kubernetes, devops, k8s, auto-scaling, devops-articles, devops-journey, pods, devopscommunity, vertical-pod-autoscaler, vpa

---

# Vertical Pod Auto-Scaler (VPA)

As I explain in previous blog, when a pod with 100 MB of RAM experiences increased load beyond 100 MB, the Vertical Pod Autoscaler (VPA) will increase the size of the pod rather than creating multiple pods.

**Steps to utilize the Vertical Pod Autoscaler (VPA) for pods in Kubernetes:**

1. Create a file named `apache-vpa.yml` for the Vertical Pod Autoscaler (VPA). This file is similar to the Horizontal Pod Autoscaler (HPA), but there are some differences you need to adjust.
    

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticaltalPodAutoscaler
metadata:
  name: apacha-hpa
spec:
  targetRef: # HPA replica target to our deployment, that's why we add apiVersion, kind, name of deployment here.
    apiVersion: apps/v1
    kind: Deployment
    name: apache-deployment
  updatePolicy:
    updateMode: "Auto"   #For Automatic Scaling
```

Here in VPA, `apiVersion` should be `autoscaling.k8s.io/v1` as mentioned into Kubernetes documentation.

2. You can apply the changes
    

```bash
kubectl apply -f apache-vpa.yml
```

3. It will not apply the changes because it is asking to install CRSs first before applying VPA changes.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735577037305/a1e726f6-e3b3-4829-b0f9-2e85b5b80359.png align="center")

CRDs (Custom Resource Definitions) are like plugins for Kubernetes. They let you add new types of resources that Kubernetes doesn’t support by default.

For example:

* Kubernetes knows about Pods, Services, and Deployments.
    
* But if you want to use a `VerticalPodAutoscaler`, you need to install its CRD first.
    

Think of CRDs as a way to teach Kubernetes about new things it can manage.

4. Let's install the CRDs. You can find the installation steps in the repository, or follow the steps below:
    

```bash
cd ../../

git clone https://github.com/kubernetes/autoscaler.git

cd autoscaler/vertical-pod-autoscaler

./hack/vpa-up.sh   #This fill will install all the required things for VPA
```

Now, verify that the VPA pods are available in our kube-system.

```bash
kubectl get pods -n kube-system
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735577954270/a16e2dc9-37fa-41d6-9249-638969e4a717.png align="center")

So, do you get the idea now? Your cluster didn't know what VPA was.

5. Now, try to install your `apache-vpa.yml`, and you will see it is created.
    

```bash
cd /kubestarter/HPA_VPA

kubectl apply -f apache-vpa.yml
```

6. Check
    

```bash
kubectl get vpa
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735583116089/edf6ad02-6507-4a5d-9ca8-4731af231cd5.png align="left")

7. You can see the CPU usage is 25 MB, and the application is running smoothly. So, let's try to generate load using BusyBox, as we did in the HPA blog.
    

```bash
kubectl run -i --tty load-generator --image=busybox /bin/sh

#Inside the container, use 'wget' to generate load:

while true; do wget -q -O- http://apache-service.default.svc.cluster.local; done
```

8. Now that the load is being generated, you can monitor the VPA in another terminal. As the load increases, you will see how the CPU usage goes up and the memory increases automatically.
    

```bash
watch kubectl get vpa
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735584173905/40185fb3-bf64-449f-b6dc-1287e576fdc1.png align="center")

You can see our application is taking lots of load.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735584524363/73cf987a-68be-4e3c-af4b-90c8a915db61.png align="left")

This is how the Vertical Pod Autoscaler operates.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).