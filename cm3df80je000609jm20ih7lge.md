---
title: "Deploy in 30 Minutes: Zero-Touch Setup with Jenkins, Docker, and Agent Configuration"
datePublished: Mon Nov 11 2024 19:33:05 GMT+0000 (Coordinated Universal Time)
cuid: cm3df80je000609jm20ih7lge
slug: deploy-in-30-minutes-zero-touch-setup-with-jenkins-docker-and-agent-configuration
tags: docker, projects, jenkins, docker-images, jenkins-devops, agent, jenkins-installation, jenkins-pipeline, jenkins-agent, deplyment-in-30-mins

---

Hey there! If deploying apps feels like a headache, this guide is for you. I’ve shared exactly how to set up Jenkins, Docker, and agents so you can deploy your app with just one click no hassle, no fuss. I also put together a **GitHub** project you can follow, and for more details, check out my full blog post here. In just 30 minutes, you’ll have a hands free pipeline up and running!

Follow below steps to execute the project with setup of Jenkins, AWS and Docker with pipeline creation.

* **Go to AWS** – Open your AWS Management Console and sign in with your credentials.
    
* **Go to Instances** – In the AWS Console, locate and click on "EC2" to access your instances.
    
* **Click on Launch Instance** – Select "Launch Instance" to begin setting up a new instance.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731248669783/9503426e-26d3-4513-8dc1-79788b9f54bb.png align="center")

* Name the instance `jenkins_server` and select `Ubuntu` as the OS image.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731248818824/effe83c2-ffcb-4c73-b709-a0d4e177e0ea.png align="center")

* Select the instance type as `t2.micro` *(choose Free tier only, so it won’t cost you anything).*
    
* Create a new `.pem` key pair. This will download the key.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731248896377/986baeb3-cd0e-4bea-9d79-387b6d31eda3.png align="center")

* Enable traffic for `SSH`, `HTTP`, and `HTTPS`.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731248945983/36d4ae05-747e-4549-b5d7-170709cf13a4.png align="center")

* 8 GB of storage is sufficient.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731248981425/26ac7fae-067e-409e-bb17-4a21a1c973b8.png align="center")

* Add 2 Instance: Master and Worker(Agent).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731249007503/f70a832e-510b-4fec-b523-705e68349f80.png align="center")

* Now rename the instances as `jenkins-master` & `jenkins-worker`.
    
    *Note: The* `jenkins-worker` *is simply an* `agent`*.*
    
* Now open each instance in separate tabs and click on `Connect`.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731250404914/3640d5d7-0b66-4371-bc51-b9c66d3627fe.png align="center")

* Open your terminal. I'm using Mobaxterm (Windows).
    
* Navigate to the folder where you saved the `.pem` key created during instance setup.
    
* Set permissions for the `.pem` key as shown in point number 3 in the screenshot below.
    
* Now run the `ssh` command for the master and worker in two separate tabs.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731250479383/b5b3dc1f-bd51-4ceb-a60a-1ee6c68e8be4.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731250341174/e6f1e902-89df-41e1-a41e-b34effd6a017.png align="center")

* **Java** should be installed on both instances.
    
    *Note: Refer to this documentation* `( Jenkis Installation )` *for the installation of Jenkins.*
    
    ```plaintext
    sudo apt update
    sudo apt install fontconfig openjdk-17-jre
    ```
    
* Now, we need to install Jenkins only on the Master instance.
    

**WHY install Jenkins on Master Instance only?**

\- <mark>We install Jenkins on the master so it can manage and assign tasks, while agents focus only on running those tasks efficiently.</mark>

```plaintext
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

* Now install Docker and Docker Compose on both the instances:
    

```plaintext
sudo apt-get install docker.io docker-compose-v2
```

* Now, give the `jenkins` user permission to join the `docker` group on the Master Instance.
    

```plaintext
sudo usermod -aG docker jenkins
```

* Now, give the current `$USER` permission to join the `docker` group on the Worker Instance.
    

```plaintext
sudo usermod -aG docker $USER
```

* Now run `sudo newgrp docker` on both instances to refresh the groups.
    
* Now connect to the `jenkins-master`. Copy the IP address and add port `8080` in the browser.
    

*Note: 8080 is the port number for Jenkins.*

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731251980091/2c08145b-1dc1-4c9c-8e3a-44c06573fefe.png align="center")

* Before connecting, you need to add an **Inbound entry** for port range `8080` with the source set to `My IP` in the jenkins-master instance.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731252131791/51f60d3f-0ee2-4b84-b497-662bec318b4f.png align="center")

* Now run the command below, so that if our instance reboots, Jenkins will start automatically.
    

```plaintext
sudo systemctl enable jenkins
```

* Open your browser, and the window below will appear. You need to unlock Jenkins.
    
* Run `cat /var/lib/jenkins/secrets/initialAdminPassword` on the jenkins-master to get the password.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731252549906/b5748201-a5fa-4275-a9f3-e26567ec36c2.png align="center")

* Always remember, we only need to setup the master once and then send work to the worker.
    
* **Agent** concepts are based on **SSH**. If we want to connect from server\_a to server\_b, we use SSH concepts.
    
* Create `admin` user:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731253340582/e2450d46-2216-491a-96d1-f27e52d33cf0.png align="center")

* Once the setup is complete, click on "**Set up an agent**"
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731253482701/d2100cc8-41f7-4be1-9c4c-8d8c70f3f295.png align="center")

* Create a new node worker (Agent).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731254321903/f3795b0d-7289-4b25-9ac2-f49125a35182.png align="center")

* What is the "Number of executors"?
    
    * If you want to perform multitasking or run multiple builds, you can add **n** number of executors. Here, we are assigning 1 executor.
        
* What is the "Remote Working Directory"?
    
    * This is the directory where you want to store all the Jenkins jobs on the Worker Instance.
        
* What is a "Label"?
    
    * When we write `agent any` in a pipeline, it will run tasks on any available agent.
        
    * But if you want to run tasks on a specific node, you need a specific label name.
        
* What is “Usage“?
    
    * Used for multi-branch pipelines like prod, dev, and stage. It only builds jobs with label expressions that match.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731255104337/30fe6c2e-f9bc-4b9e-9001-14ff15267cb6.png align="center")

* Worker(Agent) IP:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731254623526/f75171ae-3977-49a3-a2d8-ba194c0d91f7.png align="center")

* Credential details:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731254929265/1b75a534-2e45-4515-af3c-8c060f8b9c28.png align="center")

* What is the "Host Key Verification Strategy"?
    
    * If your hosts are unknown to each other, use the "Non-verifying Verification Strategy." It will verify using SSH.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731255308477/94a062c5-9924-4689-9d2e-b424cd4d0bb2.png align="center")

* Now save. Your dev-server is in sync.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731255484715/c47c1f46-dff1-4c80-80af-53f2b764b896.png align="center")

* Now, if you go to the Jenkins worker (agent instance), you will see some folders and files. These has been created using Jenkins.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731255592446/623502f7-6c99-4348-8a3c-1a1d5c3c7528.png align="center")

* Now we can assigns tasks to our `jenkins-worker`.
    

Go to Jenkins Dashboard and click on “**Create a job**".

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731344452852/b03093a8-e79d-4751-9ca9-8d7e3c1b6c86.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731347609660/6580c709-5cf2-4df1-ae23-c5dc7afc5440.png align="center")

* Now add the GitHub project. (I have added the **node todo CICD** project.)
    
    * You can also refer to this project: [https://github.com/Chetan-Mohod/node-todo-cicd](https://github.com/Chetan-Mohod/node-todo-cicd)
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731347970291/29ba29a0-462c-4bd5-8fb3-fe792fd9f66f.png align="center")

* Now add below groovy code into pipeline block:
    

```plaintext
pipeline {
    agent { label 'dev-server' }
    stages {
        stage("Code Clone") {
            steps {
                git url: "https://github.com/Chetan-Mohod/node-todo-cicd.git", branch: "master"
            }
        }
        stage("Code Build & Test") {
            steps {
                sh "docker build -t node-app ."
            }
        }
        stage("Deploy") {
            steps {
                sh "docker-compose down && docker-compose up -d --build"
            }
        }
    }
}
```

* After saving above code, go to Dashboard and run the Node.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731350002473/3437dc77-a6b2-405a-ba69-8a0cf0d7e682.png align="center")

* Now you can see `Built-In-Node` assigning its task to `dev-server`. It has been deployed, as shown in the image below.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731350372461/25048ec2-e475-4023-9d18-07fcd154642c.png align="center")

* Now to check you’re application is running on jenkins-worker.
    
    * Go to AWS → Instances → select jenkis-worker → Security → Edit Inbound Rules → Add Port 8000 , Anywhere-IPv4 → Save
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731351033030/332bfdcc-e985-4946-b453-b53e89580788.png align="center")

* Now copy the public IP from `jenkins-worker` and paste it into the browser with port 8000 to run the application.
    
    * http://**jenkins\_worker\_public\_ip**:8000
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731354381574/fd42cf7d-ac3b-46f3-bcb4-9d670a711500.png align="center")

Congratulations! Application is running successfully.

### Thanks for reading the blog! Happy Learning! :)