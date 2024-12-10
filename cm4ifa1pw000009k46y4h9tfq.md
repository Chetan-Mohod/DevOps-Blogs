---
title: "Effortless Automation with Ansible: Control Servers, Save Costs, and Optimize Resources"
datePublished: Tue Dec 10 2024 12:13:13 GMT+0000 (Coordinated Universal Time)
cuid: cm4ifa1pw000009k46y4h9tfq
slug: effortless-automation-with-ansible-control-servers-save-costs-and-optimize-resources
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733832631250/8905906b-4973-48a7-ade6-094ba306ce60.png
tags: server, ansible, automation, devops, configuration, iac, devops-articles, devops-journey, 90daysofdevops, ansible-playbook, 90daysofdevops-chanllenge, 90daysofdevops-devops-projectdevelopment-nonitbackground-github-docker-cloudplatforms-ec2-aws-elasticbeanstalk-lambdafunctions-devopspipelines-terraform-jenkins-docker-devsecops-scm-git-gitlab-bitbucket-buildtools-griddle-maven-ant-msbuild-monitoringtools-prometheus-grafana-ansible-ai-chatgpt-valueaddition-realworldproblems, 90daysofdevopschallenge, automation-tools, devopscommunity

---

> It is recommended to read this blog before proceeding with the current one: to understand what is [Ansible](https://hashnode.com/preview/6755c02dbd8aa3a52afe7239).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733682253242/67116b97-7763-499d-9da3-3884a24f8630.png align="center")

We will create the same architecture as shown in the image above.

Steps to create instances:

1. Visit the AWS Console and launch an EC2 instance:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733682683103/ad894548-cc5f-45d1-bc46-5121627df869.png align="center")

2. We will create 2 Ubuntu instances and 1 Amazon Linux instance.
    
3. First, we will create 2 Ubuntu instances. Follow the same steps as shown in the screenshot.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733682813931/da22f323-fff7-477a-bc5b-2a9a03874aab.png align="center")

4. Now that we have two Ubuntu instances, we will rename one to `ansible-ubuntu`.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733683012912/7884b6d9-9283-45b2-bb29-1efcadaec986.png align="center")

5. Now we will launch Amazon Linux al2 Instance:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733683210337/2561dda2-f2a6-4f05-b1ec-b4847294c4fd.png align="center")

You can observe that there are three instances running: one Master server and two additional servers.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733683269412/1b1cc3bd-7d53-450f-9e78-7b073242d032.png align="center")

6. As DevOps Engineers, our tasks are focused on the Master Server. To proceed, click on the Master Server and connect using SSH.
    

### Installation of Ansible:

Refer this documentation to install [Ansible](https://docs.google.com/document/d/1aepYrnL38oN5_LCvmJFY_Q-ez3iExQHq98XCUzeW3NE/edit?tab=t.0#heading=h.aczyuw2yex2w).

Perform the following steps on the server to install Ansible:

As we can see, Python is required for Ansible. Therefore, we will add a Python repository.

```bash
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt-get install ansible
```

Now run below command to check the version for Ansible and configurations.

```bash
ansible --version
```

Now, let's focus on the most important file: the <mark>hosts</mark> file.

### What is the Host File?

The Master server contains all the information about the other servers, which is known as Host Information. (Host refers to Servers)

### How to Remember the File Location:

For any Linux configuration file located in `/etc/`, you can find the Ansible configuration file in `/etc/ansible/`. Here, you will see the `hosts` file.

To connect to Ansible servers, you need to access the file located in `/etc/ansible`, named `hosts`.

The Master server has Ansible installed on it. The Master server contains a hosts file, which should include information about our two servers: Ubuntu and Amazon Linux.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733752944264/70a4e961-3c82-4bb2-a68a-5da7b1e819c7.png align="center")

**Ungrouped vs. Grouped Hosts in hosts file:**

1. **Ungrouped Hosts:**  
    These are individual systems listed without any group. For example:
    

```bash
host2.example.com
```

They don’t belong to a specific category but can still be targeted by name.

2. **Grouped Hosts:**  
    Systems can be grouped under a name for easy management. For example:
    

```bash
[webservers]
web1.example.com
web2.example.com
```

Now, you can target all `webservers` at once instead of listing them individually.

Think of it as a way to organize your systems like categories in a to-do app some tasks stand alone, and others belong to specific lists.

Now, we will inform the hosts file that there is a group named **Servers**, which includes the IP addresses of our two servers.

Let's add this information to the file:

```bash
cd /etc/ansible/

sudo vim hosts
```

Navigate to AWS and copy the public IP addresses for the Ubuntu and Amazon Linux 2 (AL2) servers. Then, add these IP addresses to the following file:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733753469442/45ccf34d-deea-4fa8-b4e3-319f7c041de3.png align="center")

```bash
#Inside hosts file we will add our two servers entry
[servers]
server1 ansible_host=3.252.146.73
server2 ansible_host=3.250.114.79
server1 ansible_user=ubuntu
server2 ansible_user=ec2-user

#Assign variables to these servers
[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key_file=/home/ubuntu/keys/ansible.pem
```

Code Explanation: Documentation for [Variables](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html)

* `server1, server2`: These are names you can assign to the servers, like server1 and server2, or any other names you prefer.
    
* `[all]`: This denotes all groups and servers. Here, we will assign variables to our servers.
    
* `[all:vars]`: This is used to specify variables for all servers.
    
* `ansible_python_interpreter`: This specifies the Python interpreter that will be used for all servers.
    
* `ansible_user=ec2-user`: When accessing an Amazon Linux instance, the default username is `ec2-user`.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733754791118/a3edf2be-4875-4780-81d4-1f9bed854eed.png align="center")

When connecting to an instance, the username is specified in the SSH command, as shown in the image below:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733755381747/f64cd547-b202-4d2a-8426-1d0e864d1ee0.png align="center")

* `ansible_ssh_private_key_file`: This key is used to connect to all the Ansible servers.
    
    * Our private key is stored locally, so we will transfer it to our server using the `scp` command.
        
    * First, create the directory `/home/ubuntu/keys` on the Master server to store the `private_key`.
        
    * Then, execute the following command on your local machine where the private key is located.
        
        ```bash
        scp -i ansible.pem ansible.pem ubuntu@ec2-3-255-223-69.eu-west-1.compute.amazonaws.com:/home/ubuntu/keys
        ```
        
    * Explanation of the `scp` command:
        
        `scp -i private_key master_server_user@ip:master_server_key_folder_path`
        
    * Now, add the path of this private key directory to our hosts file.
        
* To verify all the inventories, it is important to run this command every time you make changes to the hosts file.
    

```bash
ansible-inventory --list
```

This command will check for any errors in our file. If everything is correct, it will display the output in a tree structure format.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733766549581/8bac24a2-1008-4da5-af24-5545e67a091b.png align="center")

* Now if your system wants to connect to servers let’s try to check with ping command
    

```bash
ansible servers -m ping 
#-a = Ad-hoc, like df-h, date, free -h
#-m = module, its value is already set. It is used to ping pong between 2 servers as
```

You’ll receive o/p like this

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733771198975/93a21ee9-88ec-4157-88f6-22f39af84ef8.png align="left")

Now, we can execute some Ad-hoc commands using Ansible:

```bash
ansible servers -a "free -h"
#-a = Ad-hoc like df-h, date, free -h
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733771442121/4f365bcb-4404-4b9f-becd-91b9e671a5d1.png align="center")

```bash
ansible servers -a "uptime"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733771527990/b6923122-9cda-44ab-a954-f160586f40c8.png align="left")

Now, let's proceed to create our fourth instance:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733772218401/12388648-3e4b-4034-a60d-7ac4cdbf0875.png align="center")

Now, send the instance we created to the Master server from your local machine using the following command:

```bash
scp -i ansible.pem ansible-redhat.pem ubuntu@ec2-3-252-241-164.eu-west-1.compute.amazonaws.com:/home/ubuntu/keys
```

Add an entry for our Redhat server code in the `/etc/ansible/hosts` file.

```bash
[servers]
server1 ansible_host=3.252.93.226 ansible_user=ubuntu
server2 ansible_host=34.250.19.185 ansible_user=ec2-user
server3 ansible_host=54.170.40.204 ansible_user=ec2-user
server3 ansible_ssh_private_key_file=/home/ubuntu/keys/ansible-redhat.pem

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key_file=/home/ubuntu/keys/ansible.pem
```

Verify all the inventories:

```bash
ansible-inventory --list
```

Now, you can attempt to ping server3 as well as all the other servers.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733773867407/d63f59da-1fc5-4eff-b5cc-7d00b1405b0f.png align="left")

We have now successfully connected all three servers to our Master server using Ansible.

You will also receive the Ad-hoc execution time.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733774167621/c4dc6c08-44a6-46a2-a468-8fef53e32243.png align="left")

---

## Power of Ansible:

The primary use of Ansible is to manage configurations. Let's explore how to do this:

### **Ubuntu**

* Let’s try to run nginx on server1 from Master server.
    

```bash
ansible server1 -a "sudo apt-get update"

ansible server1 -a "sudo apt-get install nginx"
```

Now, copy the public IP of server1 from AWS and enter it in your browser (`http://public_ip`) to view the results.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733774707529/980bdca7-a38b-401b-a1fa-d2a4a6e3e197.png align="center")

Now, without directly accessing server1 you can see that nginx has been installed

---

### **RedHat**

To install the httpd service on RedHat and test it, follow these steps:

```bash
ansible server3 -a "sudo dnf install httpd -y"
```

Here, `dnf` is the package installer used in RedHat. To check the services on the RedHat server, you can use the following command:

```bash
ansible server3 -a "sudo service httpd status"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733817206350/8e77dba9-2a11-4d41-8503-cd3ca4d6347b.png align="center")

The service is not running, so we need to start it.

```bash
ansible server3 -a "sudo service httpd start"
```

You can verify that it is functioning correctly:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733817570324/249fa7b7-6d66-4116-8269-7b32191ab42e.png align="center")

### **Amazon Linux: CentOS**

Perform the same steps as for RedHat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733817659559/26e20f16-9c32-410e-884d-97053a22f3af.png align="center")

**This is the power of Ansible. It allows us to install or run anything on all servers from the Master server.**

### Happy Learning :)

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [LinkedIn](https://www.linkedin.com/in/chetanmohod/).