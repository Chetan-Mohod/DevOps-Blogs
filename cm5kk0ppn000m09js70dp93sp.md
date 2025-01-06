---
title: "Monitoring Magic: How Prometheus and cAdvisor Keep Your App in Check!"
datePublished: Mon Jan 06 2025 04:41:10 GMT+0000 (Coordinated Universal Time)
cuid: cm5kk0ppn000m09js70dp93sp
slug: monitoring-magic-how-prometheus-and-cadvisor-keep-your-app-in-check
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736138219963/ddb40e5a-424d-4185-8b20-2e25e7372606.png
tags: docker, aws, kubernetes, monitoring, prometheus, dockerfile, docker-compose, docker-images, time-series-database, docker-network, 90daysofdevops, 90daysofdevops-chanllenge, monitoring-tool, prometheus-installation, monitoring-using-prometheus-and-grafana-on-aws-ec2

---

### **Monitoring (<mark>WHY</mark>** is your application slow, off, etc)

**Definition:** Collecting and analyzing metrics over time to identify system performance trends, availability, and resource utilization.

**Purpose:** Alerts when predefined thresholds are breached, ensuring system reliability.

**Example Metrics:** CPU usage, memory consumption, request latency, error rates.

**Tools:** Prometheus, Datadog, Grafana.

**Today, we will explore Prometheus.**

---

### **What is Prometheus?**

* Prometheus is a Timeseries Database.
    
* Timeseries DB: A time series database (TSDB) stores data points collected over time, with each entry having a timestamp. It is designed to analyze trends or patterns, such as CPU usage or stock prices, over time.
    
* Prometheus includes components like a query server to store data. The scraper collects data from various sources and stores it in the data store, while the query server provides output to the user.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735989861743/feaaa9f8-bcf8-44f2-b956-c4535866023a.png align="center")

* SoundCloud originally developed Prometheus as a time series database for monitoring. Later, Prometheus was contributed to the Cloud Native Computing Foundation (CNCF), where it became an official tool and was released as open source.
    
* Prometheus is a time series database that gathers data from various sources. It includes a query server that processes this data and sends the output to visualization tools like Grafana.
    

### What is Grafana?

* Grafana is a tool for visualizing and monitoring data. It creates dashboards and graphs to help you see and understand your data from sources like Prometheus or databases.
    

---

### **Create a Prometheus and Grafana stack, deploy an application, and monitor it.**

Lets see the practical:

Steps:

1. Follow below steps to create an EC2 instance:
    

* Assign any name to the instance and select the Ubuntu AMI.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736007280899/0caa6589-b358-4042-9f96-671b208bef8e.png align="center")

* Select the t2.medium instance type because running both Prometheus and Grafana requires at least this level of resources. Additionally, create a `.pem` key for secure access.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736007356595/dbfba5b5-dfd5-42ce-81e4-c2cac8e4c637.png align="center")

* Allow HTTP and HTTPS traffic and allocate 15 GB of storage and click on Launch instance.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736007492934/4f624937-c3a6-4b9f-8ea7-683bc0684552.png align="center")

* Connect with the Instance via SSH.
    

2. Update the system and install Docker. Then, create a directory named "observability."
    

```bash
#Update the system
sudo apt-get update

#Install docker and docker compose
sudo apt-get install docker.io docker-compose-v2 -y

#Give permission to newly created group
sudo usermod -aG docker $USER && newgrp docker

#Check the containers
docker ps

#Check docker compose version
docker compose version

#Create a directory
mkdir observability

cd observability
```

3. To begin monitoring, we need an application. We will use our [django-notes-app](https://github.com/Chetan-Mohod/django-notes-app) for this purpose.
    

```bash
git clone https://github.com/Chetan-Mohod/django-notes-app.git

cd django-notes-app/

#Switch from main branch to dev branch
git checkout dev
```

4. Now create a Docker Image:
    

```bash
#Create an image
docker build -t notes-app .

#Create and start a container
docker run -d -p 8000:8000 notes-app
```

5. Go to our AWS EC2 instance → Security Rules → Edit Inbound Rules → Add an entry for port 8000. As shown in below image.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736009193408/691aab50-9a30-4a2f-8ed9-fac57fd6ed5c.png align="center")

Take the public IP address and enter it into the browser, followed by port number 8000.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736009450690/b1b1ae35-5faa-4fda-8c66-c9172805d57d.png align="center")

You can see the app is running:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736009502306/cb30b9cb-31ac-4482-bc99-9391bd2c8e20.png align="center")

```bash
#Docker stop

docker stop container_ID
```

6. Create `docker-compose.yml` & run:
    

```yaml
version: "3.8"

services:

  notes-app:
    build:
      context: django-notes-app/.    #Dockerfile is inside django-notes-app
    container_name: notes-app
    ports:
      - "8000:8000"
```

```bash
docker compose up
```

You can see that the application is running with Docker Compose. We will automate our application using only Docker Compose.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736013672993/09a05f53-9fef-47f4-9c0a-b5fc7139c34a.png align="center")

7. Now we will move on to checking the logs, as shown in the image below.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736014023352/d7b11c25-1c52-4989-a585-756c5028fefc.png align="center")

8. Where do you check Docker logs? —&gt; The answer is Prometheus.
    

**Let's proceed with the following steps:**

Where can we find Prometheus?

* Open your browser and search for [Prometheus Docker Compose](https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/docker-compose-linux/).
    
* We need to add the Prometheus service/container to our `docker-compose.yml` file.
    

```yaml
#vim docker-compose.yml

version: "3.8"

networks:  #For the communication of two containers (notes-app & prometheus)
  monitoring:
    driver: bridge

services:

  notes-app:
    build:
      context: django-notes-app/.    #Dockerfile is inside django-notes-app
    container_name: notes-app
    ports:
      - "8000:8000"
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    networks:
      - monitoring
```

**Explanation:**

Prometheus uses the `prometheus.yml` file to configure scrape jobs, which define where Prometheus collects metrics from (like targets and intervals). By default, this file should be at `/etc/prometheus/prometheus.yml` inside the container. Using the `volumes` line in `docker-compose.yml`, you map your custom `prometheus.yml` from the host to the container, allowing you to define or update scrape configurations easily without modifying the container.

First, we need to install or download the file. How do we create it? [Refer to this documentation](https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/docker-compose-linux/#step-2-create-the-prometheus-configuration-file).

```yaml
#prometheus.yml

global:
  scrape_interval: 1m

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 1m
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'docker'
    scrape_interval: 1m
    static_configs:
      - targets: ['localhost:8080']
```

Every minute, `prometheus` job name hitting on `localhost:9090` and gathering the data.

9. Now our configuration file has been created with the name `prometheus.yml`, now we can add this file into our prometheus container(`docker-compose.yml`).
    

```yaml
version: "3.8"

networks:  #For the communication of two containers (notes-app & prometheus)
  monitoring:
    driver: bridge

services:

  notes-app:
    build:
      context: django-notes-app/.    #Dockerfile is inside django-notes-app
    container_name: notes-app
    ports:
      - "8000:8000"
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    networks:
      - monitoring

    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"
```

```yaml
docker compose up -d
```

Docker Compose is working. To verify this, expose 9090 in your inbound rules on AWS EC2 instance.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736018620179/9befa89e-bebc-4c4c-ae65-f3ccd370e753.png align="center")

Then copy your `public_ip`, paste it into your browser, and check the results. You should see the Prometheus Time Series Database. Prometheus server is up & running.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736018707912/c6787ba5-5ee5-4bcf-b324-96611f33a986.png align="center")

10. How to get data on this server?
    
    * To access the data, navigate to `https://public_ip:9090/metrics`. Here, you will find the metrics data stored by Prometheus.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736018909230/410a9abf-f918-46b6-8f9d-1648f679f5b3.png align="center")

* So if you want to see this data in prometheus
    
* Click on Stats→ Target health. Target health will show all the end points
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736073639006/0b5ec8fc-5508-41bc-881f-9fb831143aed.png align="center")

* It will display that `localhost:9090` is active, which indicates that Prometheus is running. However, our Docker setup is not operating on `localhost:8080` because there is no Docker service there. The services running inside the Prometheus container are accessible via `localhost`. If a service is running in a different container within the same network, you can access it using the `container_name`.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736073720931/5c20c537-9b3f-4db7-8a00-7f4f484494fe.png align="center")

* Here's an important point to consider: different Docker containers may run on various ports, such as 80, 8080, 9000, and 3000. How many endpoints can you realistically add? For example, if you have 6 Docker containers, each running on different ports, you would need to add 6 endpoints. If you have 100 containers, you would need to add 100 endpoints which is not feasible. Ideally, there should be a single solution, which is why we will use **cAdvisor.**
    
* **<mark>cAdvisor</mark>** (short for **Container Advisor**) is a tool made by Google, cAdvisor collects metrics for each container on a host and exports them to Prometheus, which centralizes and visualizes data for all containers across multiple hosts.
    

11. Lets create another service for cAdvisor: [Refer this documentation](https://prometheus.io/docs/guides/cadvisor/)
    

```yaml
#vim docker-compose.yml

version: "3.8"

networks:  #For the communication of two containers (notes-app & prometheus)
  monitoring:
    driver: bridge

volumes:   #prometheus volume
  prometheus_data:

services:

  notes-app:
    build:
      context: django-notes-app/.    #Dockerfile is inside django-notes-app
    container_name: notes-app
    ports:
      - "8000:8000"
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    networks:
      - monitoring

    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus              #Save prometheus volume here, which we declare at top
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    ports:
      - "8080:8080"
    volumes:
    - /:/rootfs:ro          #cadvisor directly access root file system due to that all container data it gathers
    - /var/run:/var/run:rw  #Data of all tghe running processes it gather
    - /sys:/sys:ro          #
    - /var/lib/docker/:/var/lib/docker:ro   #Docker related files
    depends_on:
    - redis
    networks:
      - monitoring

  redis:
    image: redis:latest
    container_name: redis
    ports:
    - "6379:6379"             #Redis port
```

cAdvisor directly accesses the root file system. This allows it to gather all container data effectively.

Here’s a concise explanation of the `cAdvisor` configuration:

### **1.** `cadvisor:`

Defines the `cAdvisor` service in Docker Compose.

### **2.** `image:` [`gcr.io/cadvisor/cadvisor:latest`](http://gcr.io/cadvisor/cadvisor:latest)

Uses the latest cAdvisor image from Google Container Registry.

### **3.** `container_name: cadvisor`

Names the container `cadvisor` for easy identification.

### **4.** `ports:`

* `"8080:8080"`: Exposes cAdvisor on port 8080 for access to the UI or metrics.
    

### **5.** `volumes:`

Mounts directories for cAdvisor to gather system and container data:

* `/-:/rootfs:ro`: Read-only access to the root filesystem for container stats.
    
* `/var/run:/var/run:rw`: Access to process runtime data and Docker socket.
    
* `/sys:/sys:ro`: Read-only access to system performance metrics (e.g., CPU/memory).
    
* `/var/lib/docker/:/var/lib/docker:ro`: Access to Docker data for metadata and resource tracking.
    

This setup allows cAdvisor to monitor all containers on the host effectively.

* cAdvisor utilizes a Redis cache to operate efficiently, which means you need to set up a Redis server. Redis is a key-value pair data store, ideal for storing temporary data. Since cAdvisor handles a large amount of container data, it uses Redis for caching purposes.
    

12. Lets run it now, first down the container then apply the changes:
    

```bash
docker compose down

docker compose up
```

* Our cAdvisor runs on port 8080. Add an entry for port 8080 in our EC2 inbound rules. Then, copy `public_ip:8080` and paste it into your browser. You can see cAdvisor is running:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736077074208/408b5675-aefc-4c49-86d4-b989616d99a7.png align="center")

* Click on Docker Containers, you can see notes-app is available here.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736077123409/8a75b6a5-4e0c-4590-b0f4-eb9fef5161bd.png align="center")

* Here you can see real time monitoring:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736077308596/ca80bb90-6585-4b81-a27f-a145a9715a35.png align="center")

* Now this real-time data cAdvisor provides to prometheus, how?
    
* In Prometheus, you may notice that it is not displaying data for our other containers, indicating they are down. To resolve this, you need to add the cAdvisor container name to the `prometheus.yml` file. This will enable Prometheus to fetch data for all containers, as it currently only works with the localhost. By including the container names for other services in the file, Prometheus will be able to access their data.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736077427166/6dbf66ab-5ca7-4107-a9f5-214c85070a74.png align="center")

13. To view the endpoint in Prometheus, we will add the container name to the `prometheus.yml` file.
    

```yaml
global:
  scrape_interval: 1m

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 1m
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'docker'
    scrape_interval: 1m
    static_configs:
      - targets: ['cadvisor:8080']   #cadvisor = container name
```

14. To restart the container, first stop it and then start it again:
    

```bash
docker compose down

docker compose up -d
```

Now, you can observe that our Docker container is running successfully.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736078177809/a4fe3f65-a5e0-4145-9f06-f33455cb4fb4.png align="center")

This is the method for adding any Docker container to Prometheus.

Now that we have gathered all the data from our target, the next step is to monitor this information. How can we do this?

15. To monitor the gathered data, click on "Query server" and enter some queries, which you can find in your browser.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736078603859/2dbf8cca-5512-4709-9745-c6d31ab1728b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736079825869/d9717798-2aef-4360-b538-224f546df701.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736079644836/8694875f-eff6-4468-9c0e-d598d77207e1.png align="center")

```bash
#Queries
rate(container_cpu_usage_seconds_total{name="notes-app"}[5m]) * 100

rate(container_network_receive_bytes_total{name="notes-app"}[30m]) + rate(container_network_transmit_bytes_total{name="notes-app"}[30m])
```

16. Now, if we have a server such as a Kubernetes cluster or a node (server), and we want to collect logs from it, we need to use a node exporter in our `docker-compse.yml` file. For detailed instructions, please refer to [this documentation](https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/docker-compose-linux/#step-2-create-the-prometheus-configuration-file).
    

```yaml
#vim docker-compse.yml

version: "3.8"

networks:  #For the communication of two containers (notes-app & prometheus)
  monitoring:
    driver: bridge

volumes:   #prometheus volume
  prometheus_data:

services:

  notes-app:
    build:
      context: django-notes-app/.    #Dockerfile is inside django-notes-app
    container_name: notes-app
    ports:
      - "8000:8000"
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    networks:
      - monitoring

    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus              #Save prometheus volume here, which we declare at top
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    ports:
      - "8080:8080"
    volumes:
    - /:/rootfs:ro          #cadvisor directly access root file system due to that all container data it gathers
    - /var/run:/var/run:rw  #Data of all tghe running processes it gather
    - /sys:/sys:ro          #
    - /var/lib/docker/:/var/lib/docker:ro   #Docker related files
    depends_on:
    - redis
    networks:
      - monitoring

  redis:
    image: redis:latest
    container_name: redis
    ports:
    - "6379:6379"

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    ports:
      - "9100:9100"
    networks:
      - monitoring
```

<mark>cAdvisor gathers container data and sends it to port 8080. In a similar manner, the node exporter collects all system or node-level information and makes it available on port 9100.</mark>

17. To connect our `node exporter` target on port 9100 to Prometheus, we need to add an entry for `node exporter` in the `prometheus.yml` file. And apply the changes.
    

```yaml
#vim prometheus.yml

global:
  scrape_interval: 1m

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 1m
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'docker'
    scrape_interval: 1m
    static_configs:
      - targets: ['cadvisor:8080']    #cadvisor = container name

  - job_name: 'node'
    scrape_interval: 1m
    static_configs:
      - targets: ['node-exporter:9100']  #node-exporter = container name
```

```bash
docker compose up -d
```

* The node exporter is now visible in Prometheus.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736094514418/e528f6d6-feae-4d27-a4ef-cdbe38889b6c.png align="center")

* Now we can run queries by using these [commands](https://github.com/DevMadhup/Grafana-monitoring/blob/main/Docker-Container-State.md) or browse through the commands:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736095483645/aa3a92ea-3dcc-48ed-9651-f7ba456e018a.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736095809422/968b32e8-249b-4f61-92ae-dffa387335fe.png align="center")

```bash
#Stop our containers
docker compose down
```

* You might be wondering how to remember these queries. It's not easy to memorize them. Therefore, we will use Grafana to display all this data. Grafana does not require you to remember queries and will automatically create an impressive dashboard for everything.
    

* We will explore the integration of Prometheus and Grafana in our next blog.
    

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).