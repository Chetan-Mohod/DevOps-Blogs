---
title: "Grafana: The Ultimate Guide to Beautiful and Effective Monitoring Dashboards"
datePublished: Tue Jan 07 2025 04:26:42 GMT+0000 (Coordinated Universal Time)
cuid: cm5lyxyjk000209jm0vvv6zhz
slug: grafana-the-ultimate-guide-to-beautiful-and-effective-monitoring-dashboards
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736102505752/8ae20e34-c7b8-429c-a333-f47a79fb84fc.png
tags: cloud, technology, monitoring, devops, sre, grafana, site-reliability-engineering, devops-articles, devops-journey, 90daysofdevops, 90daysofdevops-chanllenge, 90daysofdevopschallenge, monitoring-tool, devopscommunity, grafana-monitoring

---

### What is Grafana?

* Grafana is a tool for visualizing and monitoring data. It creates dashboards and graphs to help you see and understand your data from sources like Prometheus or databases.
    

> It is essential to read the previous blog of [prometheus](https://hashnode.com/preview/677912be65a0f5e2142e9205) before continuing with this one, as it provides necessary context for understanding the current content.

You can refer to this [document](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/) to write the Grafana service in `docker-compose.yml`. If you have been following my blogs, you will know that memorizing code is not necessary. Instead, we just need to consult the documentation and make minor adjustments to the code, such as changing the namespace and volume name.

Steps for Integrating Prometheus into Grafana:

1. To integrate Prometheus into Grafana, begin by adding the Grafana service to the `docker-compose.yml` file:
    

```yaml
#docker-compose.yml

version: "3.8"

networks:  #For the communication of two containers (notes-app & prometheus)
  monitoring:
    driver: bridge

volumes:
  prometheus_data:     #prometheus volume

  grafana_data:        #grafana volume

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

  grafana:
    image: grafana/grafana-enterprise:latest
    container_name: grafana
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - monitoring
    restart: unless-stopped

    depends_on:
      - prometheus
```

Implement the changes.

```bash
docker compose up -d
```

2. Add the Grafana 3000 port entry to the inbound rules of our EC2 instance. After adding the port entry, copy the public IP of the EC2 instance and enter it into the browser, followed by port 3000, as shown below.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736098830883/171c7d77-8a7f-4d96-a1ce-ed2064115c3f.png align="center")

The Grafana server is now up and running.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736098944827/2d85a103-2ae8-4a7f-82bc-14a69eb129ac.png align="center")

3. Log in with the new user, and you will see the Grafana dashboard displayed as shown below:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099040989/23430f0f-0273-4ad7-ba54-40cdac3decc9.png align="center")

4. We want to do connection between Prometheus and Grafana so click on Connections → Add new connection → Search “Prometheus“ & click.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099198570/325b3883-fd64-4d26-af9b-10b48ccd9d43.png align="center")

* Click on **Add new data source**:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099413501/875226ac-9799-477f-87fc-6cb98777d77e.png align="center")

* Enter the URL of the Prometheus server:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099541125/14a3f80d-0c9a-418e-99f2-da3fc9875e48.png align="center")

* Click on **Save & test**, then proceed to **building a dashboard** to start creating a new dashboard.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099629676/720f3ef4-1f3a-4a55-984c-ebe2f397f988.png align="center")

* Click on **Add Visualization:**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099714467/ba6c0ffa-5f18-4b3c-b4ad-53422eed2b98.png align="center")

* Click on **Prometheus** data source:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099764572/78be940e-d025-4782-b72c-e1500b68b38d.png align="center")

* Create a query by clicking on **Select metric**:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736099880792/060cb64f-fd9a-4312-b167-2f9bca5f24ae.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736100110513/c17de62e-6b89-46d4-b747-609f537d773f.png align="center")

* To add visualization designs, click on Time series and follow the suggestions provided:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736100185259/2804cc46-1255-4360-aed1-bbc42db96203.png align="center")

* Click on "Back to Dashboard," located at the top right side of the menu bar.
    
* Click on Add → Visualization.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736100568050/5c39e15a-2319-4493-8c35-fdd64ef4924a.png align="center")

* Add another visualization and click on back to dashboard:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736101279453/a187058e-dadb-4d10-8bea-d67c51a45c42.png align="center")

* You can see it is available on dashboard.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736101360599/df75523b-c55a-4f77-b7c1-7216eb399e74.png align="center")

* Now we can check the errors from the last 30 minutes and add a visualization for that too:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736101502243/8561d14b-a398-4483-bbab-c225792e9897.png align="center")

* Here you can see around 80% our CPU has been used, so if we added more 2-3 services in our docker compose then our CPU will be full. This is how visualization helps.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736101896151/8c5b928a-103f-4e91-9d2b-386c472fff5f.png align="center")

* This is how our Grafana Dashboard will looks like, now we can change the panel name:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736102230633/efc51c0e-5f4a-4393-97f6-bdde18e4be21.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736102271410/d2302a07-03b1-4d67-9ac4-ff7d8b5a160f.png align="center")

* Similarly, I have included a map and a News panel to improve the dashboard's visual appeal.
    
* Finally, this is the appearance of our completed dashboard:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736102484710/1e21f6ab-ca92-4353-aab9-98384db63c44.png align="center")

---

### Grafana Dashboard Templates:

There are many templates available for creating a Grafana Dashboard. You can easily find them by searching for "[Grafana Dashboard templates](https://grafana.com/grafana/dashboards/)" in your browser.

1. Search for Prometheus and select the template you like.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736102892337/df50d835-0a3e-4ac0-8dc0-b34ae5b5eb47.png align="center")

2. Click on “Copy ID to clipboard“
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736102950218/ba722fec-b1c3-46d7-8ff4-f450d395d633.png align="left")

3. Now go to our Grafana Dashboard and click on New and then Import:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103038447/e2eb87d3-55ec-41bb-96be-25bf3458c532.png align="center")

* Paste the ID and click on Load:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103094730/0c619e90-2f8c-4bfe-a72d-ecf373a3c4bf.png align="center")

* Select a Data source and Import:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103137315/2ce4848e-fc79-4456-a6d4-da095c1dc5e6.png align="center")

* You can see our Grafana Dashboard:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103212437/031eee8a-1150-4d1f-bbdc-fa1ad0ae399c.png align="center")

* Let add another dashboard for Docker:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103452823/287a462c-e853-4025-b632-fb3f7509bf15.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103420319/cb637ea6-cfa8-49d7-9234-28d6619bfc57.png align="center")

4. To check on what basis this graph has been created, click on edit and check the query:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103730159/f5d42bdc-a79b-4307-aa1c-a7da7f6d8b4f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736103743138/86571507-bf57-4e7b-bdbe-2c3829f76d56.png align="center")

This is how Grafana operates. You can monitor your application easily using Prometheus and Grafana, with enhanced visualization options.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on LinkedIn**👇**