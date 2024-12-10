---
title: "Why Choose Ansible Over Chef or Puppet: A Deep Dive into Passwordless Configuration Management"
datePublished: Sun Dec 08 2024 18:40:05 GMT+0000 (Coordinated Universal Time)
cuid: cm4fy7uny000609ib772vbggt
slug: why-choose-ansible-over-chef-or-puppet-a-deep-dive-into-passwordless-configuration-management
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733678036271/d9e20c9a-9fd4-4968-ae04-f39d652bfd15.png
tags: cloud, ansible, puppet, cloud-computing, devops, chef, devops-articles, 90daysofdevops, ansible-playbook, ansible-module, 90daysofdevops-chanllenge, 90daysofdevops-devops-projectdevelopment-nonitbackground-github-docker-cloudplatforms-ec2-aws-elasticbeanstalk-lambdafunctions-devopspipelines-terraform-jenkins-docker-devsecops-scm-git-gitlab-bitbucket-buildtools-griddle-maven-ant-msbuild-monitoringtools-prometheus-grafana-ansible-ai-chatgpt-valueaddition-realworldproblems, 90daysofdevopschallenge

---

In the past, at some companies, if developers wanted to update system configurations, they had to submit a ticket to the system administrator. Typically, there was one system administrator responsible for updating 50 systems from an office. The administrator had to wait for developers to log off before manually updating each system with scripts, taking about 2 minutes per system. This meant it would take approximately 100 minutes to patch all 50 machines.

With the introduction of DevOps, this process can be automated using Configuration Management tools (CFM). These tools allow your servers to connect to a Master server, from which you can manage all server configurations. This is known as CFM tools.

### **How is it used?**

Tools like Chef, Puppet, Ansible, and AWS System Manager are examples. There is one Master server, and the others are the servers you want to update. The Master server gathers information from all the servers, as shown in the diagram below. Updates are then pushed from the Master server to the other servers. Chef and Puppet operate on this principle, where all servers connect to the Master server to receive information, and the Master decides the necessary actions. This push and pull mechanism is similar to how Jenkins operates.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733675111362/4fbfae9f-4cba-48a0-a570-229df57b0425.png align="center")

## Ansible

Ansible is introduced when we have servers like server1, server2, and server3, and we need to update or downgrade their configurations. In this case, a push-only approach is effective. If the Master server has the SSH keys for all the servers, it can directly access them, eliminating the need for a pull mechanism. This is the method by which Ansible operates, allowing the Master server to manage all updates through SSH access.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733676369538/992a4fbd-4db5-4b73-9f7b-f9fd6cf9c210.png align="center")

Ansible operates on the straightforward concept of passwordless authentication.

Passwordless authentication means you don't need to pull information from each server individually. Instead, you provide the private key of your server to the Master server. With SSH access, you don't need to know the server's password. You simply use SSH to connect and push the changes. Tasks that previously required a system administrator to spend 100 hours updating systems can now be done in parallel using Ansible, reducing the time to just 2 minutes for updating or downgrading all systems. This demonstrates the efficiency and power of DevOps.

We create a playbook in Ansible, which can use SSH to connect to all the servers. The concept of Ansible is similar to Jenkins, where Ansible should be installed only on the Master system, just as Jenkins should be installed on the Master. However, Ansible is written in Python, while Jenkins is written in Java. Therefore, Python is required on all servers to connect with Ansible.

### **Benefits of Passwordless Authentication**

* **Security**: Reduced exposure to credential leaks.
    
* **Efficiency**: Simplifies authentication for large-scale deployments.
    

With Ansible, what once took hours of manual effort can now be achieved in minutes redefining the way we approach automation and making DevOps simpler, faster, and more reliable

### Happy Learning :)

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [LinkedIn](https://www.linkedin.com/in/chetanmohod/).