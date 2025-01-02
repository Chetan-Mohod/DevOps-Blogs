---
title: "Deploying Apps with Helm: A Simple Approach and Cheatsheet for Easy Reference!"
datePublished: Thu Jan 02 2025 19:46:11 GMT+0000 (Coordinated Universal Time)
cuid: cm5fql5hl000009kydhf78aek
slug: deploying-apps-with-helm-a-simple-approach-and-cheatsheet-for-easy-reference
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735846806250/5087f8be-0132-495b-84b2-e2bc68576cb1.png
tags: kubernetes, devops, cheatsheet, helm, prometheus, cncf, grafana, cluster, devops-articles, helm-chart, devops-journey, devopscommunity, helm-installation, kubernetes-helm

---

Helm is a **package manager for Kubernetes**. Helm is a tool that helps you manage applications in Kubernetes. Think of it like an app store for Kubernetes. It makes it easier to install, update, and manage apps in your Kubernetes environment.

### How it works:

* **Charts**: These are like packages or blueprints for apps. A chart contains all the files and settings needed to run an app on Kubernetes.
    
* **Releases**: When you install a chart, it creates a release, which is basically the app running in your Kubernetes.
    
* **Repositories**: These are like shelves that hold charts, where you can find different apps to install.
    

### Why it's useful:

* **Easier Deployment**: You don’t have to write complex YAML files by hand; Helm does it for you.
    
* **Manage Versions**: Helm keeps track of different versions of your apps, so you can update them or roll back if something goes wrong.
    
* **Customization**: You can tweak settings to fit your needs without changing the whole setup.
    

### Example:

* To install an app, you run a simple command like `helm install <app-name>`.
    

It’s a great tool to save time and keep things organized when managing many apps in Kubernetes.

---

> <mark>Note: </mark> ***It is recommended to read this*** [***blog***](https://hashnode.com/post/cm59w4izg000009mjgaotfpda) ***for EC2 instance creation*** ***before continuing with this one or create any ec2 instance with t2.medium.***

### How to Install HELM?

Refer to this [Repo](https://github.com/Chetan-Mohod/kubestarter/tree/main/HELM) for installation instructions or follow the steps below:

### Prerequisites

* Kubernetes cluster running (local, cloud, or KIND).
    
* kubectl installed and configured.
    
* Helm 3 installed. Install Helm with:
    

1. Download and install a script `get_helm.sh` :
    

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

Run `ls` to see that get-helm-3 is available in the directory.

It is a shell script to install HELM.

Now, give the script permission and run it.

```bash
chmod 700 get_helm.sh

./get_helm.sh
```

2. Install the HELM Chart of Apache:
    

```bash
mkdir helm-practice

cd helm-practice
```

```bash
helm create apache

cd apache

cd templates # here you can see all the deployment, services, ingress files already created

cd ..

#Now opn values.yaml, it have replicas record

vim values.yaml
```

Lets edit this values.yml:

```yaml
#Just change below thiungs in values.yaml file

# This will set the replicaset count more information can be found here: https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/
replicaCount: 2

# This sets the container image more information can be found here: https://kubernetes.io/docs/concepts/containers/images/
image:
  repository: httpd
  # This sets the pull policy for images.
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: 2.4
```

Now run below command to install Helm:

```bash
cd ..

helm install apache ./apache --namespace apache-namespace --create-namespace
```

You can see details in below screenshot

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735836850317/1a9353f2-48e9-4556-a834-c3bcf3577b5b.png align="center")

Explanation of the command:

* `helm install`: Command to install an app with Helm.
    
* `apache`: Name for the installation.
    
* `./apache`: Location of the chart (app files).
    
* `--namespace apache-namespace`: Sets the namespace for the app.
    
* `--create-namespace`: Creates the namespace if it doesn’t exist.
    

3. Now check the pods with the namespace `apache-namespace`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735837498404/e3cab0a3-62ab-4fe9-9c28-8bf6fa489b7d.png align="left")

This is how pods gets created with the help of HELM.

---

### Deploy node-app with Zero Code:

1. Create node-app with helm:
    

```bash
helm create node-app

cd node-app/
```

2. Update below details in `values.yaml`
    

```yaml
#vim values.yaml

image:
  repository: chetan0103/node-app
  # This sets the pull policy for images.
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: latest

-----------------------

# This is for setting up a service more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/
service:
  # This sets the service type more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types
  type: ClusterIP
  # This sets the ports more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/#field-spec-ports
  port: 8000
  targetPort: 8000
```

3. After add above details go to `templates/service.yaml` & update targetPort details:
    

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "node-app.fullname" . }}
  labels:
    {{- include "node-app.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
      protocol: TCP
      name: http
  selector:
    {{- include "node-app.selectorLabels" . | nindent 4 }}
```

4. Now you can apply the changes:
    

```bash
helm install node-app2 ./node-app --namespace node-app --create-namespace
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735838806687/4d079c01-fa0d-478c-8794-3f4a00b76a4a.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735838773014/f698bf4c-82fb-4b52-bf1a-d624010fa37a.png align="center")

5. Now try run our application:
    

```bash
kubectl port-forward service/node-app2 -n node-app 8000:8000 --address=0.0.0.0
```

Get your public IP from AWS and enter it in the browser with port 8000:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735839398150/9a84b224-55a1-450e-91e6-29812f93f553.png align="center")

This is a no-code deployment of the application we set up.

---

---

### Deploy Grafana and Prometheus Application:

Repo: [https://github.com/Chetan-Mohod/Wanderlust-Mega-Project](https://github.com/Chetan-Mohod/Wanderlust-Mega-Project)

* **Add Helm Stable Charts for Your Local Client**
    

```bash
helm repo add stable https://charts.helm.sh/stable
```

* **Add Prometheus Helm Repository**
    

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

* **Create Prometheus Namespace**
    

```bash
kubectl create namespace prometheus
```

```bash
kubectl get ns
```

* **Install Prometheus using Helm**
    

```bash
helm install stable prometheus-community/kube-prometheus-stack -n prometheus
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735843758177/3e7b6369-a1e7-43dc-ba69-24a6bc269c28.png align="center")

You can see Prometheus has been deployed.

Now we can see the deployed pods for Prometheus with the help of HELM:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735843851607/7a180b24-6405-4996-b3c0-6aba8e6a27eb.png align="center")

We can check the services as well:

```bash
kubectl get svc -n prometheus
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735843951819/ae7a6488-0212-42db-99ff-40a715881637.png align="center")

Run the application for Grafana:

```bash
sudo -E kubectl port-forward service/stable-grafana -n prometheus 80:80 --address=0.0.0.0
```

You can see our Grafana is working:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735844339563/777be309-ee3a-44d9-852f-a3e2ddb2372a.png align="center")

Delete the namespace :

```bash
kubectl delete ns prometheus
```

---

---

### **Here is the** [**Cheat Sheet**](https://helm.sh/docs/intro/cheatsheet/) **for HELM.**

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).