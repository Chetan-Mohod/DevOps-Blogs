---
title: "SpringBoot + Kubernetes StatefulSets: Perfect Combo for Reliable Banking Apps! + Concepts of configMap & Secrets"
datePublished: Thu Dec 05 2024 12:44:56 GMT+0000 (Coordinated Universal Time)
cuid: cm4bb7kr9000j08l4fmvr6hg8
slug: springboot-kubernetes-statefulsets-perfect-combo-for-reliable-banking-apps-concepts-of-configmap-secrets
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733402480928/8d0d0e1e-e138-473c-b3d8-a470cbb2b605.png
tags: deployment, mysql, kubernetes, services, cluster, kubernetes-container, statefulsets, kubernetes-installation-masternode-workernode-day31, master-node, persistentvolumes

---

We have configured a deployment for the Banking App with 3 Pods, ensuring data safety by associating the data with a PersistentVolumeClaim (PVC). The PVC is connected to a PersistentVolume (PV), which is then linked to the host to provide the necessary storage.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733164636193/b3f466ab-7a9c-496f-a3d3-d4a5ff35239b.png align="center")

When a user signs up for our Banking App and clicks register, if the Pod crashes at that moment, the data might not be saved. However, thanks to the PersistentVolume (PV), we can recover the data. The challenge arises because Pods have random names and may contain login data for that specific time, making it difficult to identify the correct Pod since it is unnamed. This issue is known as **State**. In these situations, even if you attempt to save the data, it cannot manage the user's login due to the Pods having random, unnamed identifiers. Therefore, when developing any database-related project (MySQL, PostgreSQL, MongoDB), using a deployment is not recommended, as it can lead to these problems.

To address this, you should use a **StatefulSets**. It functions similarly to a deployment but creates named pods, ensuring better management of stateful applications.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733165762150/10538b30-5b5a-4cee-9f99-5ce66be80f4c.png align="center")

In a StatefulSet, pods are created with specific names such as `mysql-0`, `mysql-1`, and `mysql-2`. Each pod maintains its own state, so if `mysql-0` is deleted, it will be recreated with the same name, preserving its state. In contrast, a deployment would create pods with random names, which do not maintain state or sequence. This is the key difference between a deployment and a StatefulSet, as StatefulSets ensure better management of stateful applications by maintaining both state and order.

To prevent data loss, it is essential to create pods in a specific sequence, which can only be achieved using StatefulSets.

Steps to Implement StatefulSets and Secrets in Kubernetes:

1. Create an EC2 instance on AWS or any cloud provider. Log in with SSH and then proceed with the next steps.
    
2. If you've been following my Docker blogs, you might have noticed that we use passwords in the docker-compose.yml file. In Kubernetes, to address this security concern, we use Secrets. \[[Docker Compose blog link](https://devops-concepts-by-chetan.hashnode.dev/docker-compose-made-easy-spring-boot-banking-app-in-no-time)\]
    
3. Create `secrets.yml`:
    

```yaml
kind: Secrets
apiVersion: v1
metadata:
  name: mysql-secret
  namespace: bankapp-namespace
type: Opaque   #Secret type for storing sensitive key-value data like passwords, encoded in base64.
spec:
  data:
    MYSQL_ROOT_PASSWORD: VGVzdEAxMjMK  # Base64 for "Test@123"
    SPRING_DATASOURCE_PASSWORD: VGVzdEAxMjM # Base64 for "Test@123"
```

* To encode your password value, you can convert them into base64 format. Here's how you can do it:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733203957566/8fadd92a-7ae9-45c4-b52f-7b8078ad6ec3.png align="left")

* But it can also decode easily right, so generally we don’t commit our secret files. But instead of keeping plain password at least encode it. And this encoding Kubernetes secrets internally decode and provide to container.
    
* In Kubernetes, `Opaque` is the default Secret type used to store arbitrary key-value pairs of sensitive data, such as passwords or tokens, in base64-encoded form for secure handling and usage.
    

5. Now, we will create `configMap.yml`. This file will include variables that contain data not requiring secure storage. It is used to store our regular variables.
    

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: bankapp-config
  namespace: bankapp-namespace
data:
  MYSQL_DATABASE: BankDB
  SPRING_DATASOURCE_USERNAME: root
```

How do these secrets and configurations reach StatefulSets in a pod? This is achieved using environment variables, as demonstrated in the `statefulSets.yml` file below.

6. To apply the changes to the secrets, follow these steps:
    

```bash
kubectl apply -f secrets.yml

#Check if it created ot not
kubectl get secrets -n bankapp-namespace
```

It displays two values in the Data section.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733254054974/e11cf218-63a4-49e1-aec6-69df0e47a12c.png align="center")

Now, we will use the describe command to view more details:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733254140817/277182ce-c4b7-450b-af73-3ac0c4948420.png align="center")

You will notice that it does not display the actual value but instead shows 8 bytes of data, as it is a secret.

7. To apply the changes to the configMap, follow these steps:
    

```bash
kubectl apply -f configMap.yml

#Check if it created ot not
kubectl get configmaps -n bankapp-namespace
```

It displays a single value in the Data section.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733254177962/e23a555f-afb8-42fb-84cd-f8664967c62d.png align="center")

Now, we will use the describe command to view more details:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733254244559/5662b6f1-e0d2-404b-bb2c-6b18ed4585b5.png align="center")

You will notice that it display the actual value.

8. Create `mysqlStatefulSet.yml`:
    

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  namespace: bankapp-namespace
  labels:
    app: mysql
spec:
  serviceName: mysql-headless
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            configMapKeyRef:
              name: bankapp-config
              key: MYSQL_DATABASE
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
        livenessProbe:
          exec:
            command:
            - mysqladmin
            - ping
            - -h
            - localhost
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          exec:
            command:
            - mysqladmin
            - ping
            - -h
            - localhost
          initialDelaySeconds: 10
          periodSeconds: 5
  volumeClaimTemplates:
  - metadata:
      name: mysql-data
      labels:
        app: mysql
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
```

To determine if our containers are ready, we use probes.

Probes are requests sent to a container to check if it is functioning properly.

* What are livenessProbe and readinessProbe?
    
    * **livenessProbe**: This is a request that checks whether your health checks are passing and if your pod is active and healthy.
        
    * **readinessProbe**: This is a request that checks if your pod is ready to handle traffic or not.
        
* What is VolumeMount?
    

```bash
volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
```

Volume Mounts: Within the pod, MySQL is running, and we are attaching it to a PersistentVolume.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733209052178/1bb9a0cb-f374-4c5c-b638-7f5941ce59d6.png align="center")

Inside the pod, MySQL is running, and its data is stored in `/var/lib/mysql`.

On the worker node, there is a path `/tmp/bankapp-data`. If you want to store the container's data on the worker node, so that the data persists even if the pod is deleted, you need to store it on the node.

To achieve this, we will attach a PersistentVolume (PV) to the path `/tmp/bankapp-data` and allocate 5Gi. To connect these paths, we need a PersistentVolumeClaim (PVC) to claim 5Gi.

We will instruct the pod to use the PV named `mysql-data`, which will bind both paths together.

9. Now we will EXPOSE the MySQL service using the following method:
    

* Since you are creating this service internally and do not want to expose it publicly, set `clusterIP` to `none`.
    

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-headless
  namespace: bankapp-namespace
  labels:
    app: mysql
spec:
  clusterIP: None
  selector:
    app: mysql
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
```

10. Now we will apply all these changes:
    

```bash
kubectl apply -f mysqlStatefulSet.yml

kubectl apply -f mysqlService.yml
```

11. Now list down for all the namespaces which we have created.
    

```bash
kubectl get all -n bankapp-namespace
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733249434360/f48b1219-179f-4ab7-a628-9005439788b0.png align="center")

Here, you can observe that the pod name is not random; it follows a proper naming convention.

**To create a StatefulSet, it is important to understand three key concepts: PersistentVolume (PV), PersistentVolumeClaim (PVC), and secrets.**

### Creating BankApp:

Now, we will create the BankApp application and store the data to verify if it is being persisted correctly.

On the worker node, we create a StatefulSet for MySQL, and attach PersistentVolumes (PV), PersistentVolumeClaims (PVC), secrets, configMaps, and services to it. Similarly, we will create the BankApp, which is a frontend application that connects to the MySQL database. For this, we will create a deployment with an attached service.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733252794735/ca104b3c-f3dd-488a-b1d4-6164c6fe7424.png align="center")

1. Now we will create `deployment.yml` for Bankapp
    

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bankapp-deployment
  namespace: bankapp-namespace
  labels:
    app: bankapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bankapp
  template:
    metadata:
      labels:
        app: bankapp
    spec:
      initContainers:
      - name: wait-for-mysql
        image: busybox:1.28
        command: ['sh', '-c', 'until nc -z mysql-0.mysql-headless 3306; do echo waiting for mysql; sleep 5; done;']
      containers:
      - name: bankapp
        image: chetan0103/springboot-bankapp:latest
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_DATASOURCE_URL
          valueFrom:
            configMapKeyRef:
              name: bankapp-config
              key: SPRING_DATASOURCE_URL
        - name: SPRING_DATASOURCE_USERNAME
          valueFrom:
            configMapKeyRef:
              name: bankapp-config
              key: SPRING_DATASOURCE_USERNAME
        - name: SPRING_DATASOURCE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: SPRING_DATASOURCE_PASSWORD
        livenessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
```

2. Create service.yml
    

```yaml
apiVersion: v1
kind: Service
metadata:
  name: bankapp-service
  namespace: bankapp-namespace
  labels:
    app: bankapp
spec:
  type: NodePort
  selector:
  ports:
    - port: 8080
      targetPort: 8080
      protocol: tcp
      nodePort: 30080
```

3. Apply the changes:
    

```bash
kubectl apply -f deployment.yml

kubectl apply -f service.yml

kubectl get all -n bankapp-namespace
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733255734800/5a9b8276-ff7b-4ac1-8e03-e44d67daea6a.png align="center")

4. You can confidently state that your application is deployed on Kubernetes.
    

```bash
kubectl port-forward service/bankapp-service -n bankapp-namespace 8080:8080 --address=0.0.0.0
```

5. Now, navigate to AWS and access your EC2 instance. Proceed to the security group settings and add an entry for port 8080, allowing access from Anywhere IPv4. Next, go to the instance details and copy the public IP address. Paste this IP address into your browser using the format: `http://public_ip:8080`. You should then be able to view the results as shown below.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733255994462/f0c860cd-e957-4130-8b13-4d3a584bc6fe.png align="center")

### Validations:

1. Now, we will register a new user and then log in with it. This step is important because we want to verify whether our data persists or disappears when we delete our StatefulSets and deployment.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733376211277/c552b603-5daf-4bf5-9914-bc37426105e7.png align="center")

2. Add some transactions:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733376317508/6329f549-4895-438d-bae4-f5d6aafac388.png align="center")

3. Now, proceed to delete and then reapply the StatefulSets and Deployment.
    

```bash
kubectl delete -f mysqlStatefulSet.yml

kubectl delete -f deployment.yml

kubectl apply -f mysqlStatefulSet.yml

kubectl apply -f deployment.yml
```

4. Now, let's proceed to run this application:
    

```bash
kubectl port-forward service/bankapp-service -n bankapp-namespace 8080:8080 --address=0.0.0.0
```

The transactions remain intact even after deleting the **StatefulSets** and **deployment**, indicating that our **Persistent Volume** is functioning correctly.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733379853992/dc9bc022-c955-443c-b9d0-62643d784834.png align="center")

### **This is the process for creating a multi-tier application in Kubernetes.**

**<mark>Note:</mark> It is not required to memorize the syntax or code;** [**Kubernetes.io**](https://kubernetes.io/) **is a valuable resource you can use. Even during the CKA exam, you are permitted to refer to it.**

### Interview Question:

* **What are initContainers?**
    

—&gt; An **initContainer** is an initializer container for a pod. Before the main/app container starts, the initContainer ensures that its tasks are completed. It functions similarly to a "dependsOn" mechanism.

### Happy Learning :)

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [LinkedIn](https://www.linkedin.com/in/chetanmohod/).