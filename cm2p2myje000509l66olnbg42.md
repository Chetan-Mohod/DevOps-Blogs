---
title: "Essential Linux Networking Commands for DevOps Engineers"
datePublished: Fri Oct 25 2024 18:34:19 GMT+0000 (Coordinated Universal Time)
cuid: cm2p2myje000509l66olnbg42
slug: essential-linux-networking-commands-for-devops-engineers
tags: linux, devops, networking, linux-for-beginners, linux-basics, devops-articles, linux-commands, devops-journey, devopscommunity

---

Networking is crucial in DevOps. Knowing commands like **netstat**, **ifconfig**, and **iptables** lets you quickly troubleshoot and secure connections. Here’s a 1-minute guide to mastering these commands!

### **Key Commands & Quick Uses**

1. **netstat**
    
    * **netstat -an**: Lists all active connections and ports great for spotting open connections.
        
    * **netstat -tuln**: Shows TCP/UDP ports in use, useful for confirming active services.
        
2. **ifconfig**
    
    * **ifconfig eth0**: Displays IP, netmask, and MAC confirm interface settings.
        
    * **sudo ifconfig eth0 down/up**: Resets the network interface, helpful for troubleshooting.
        
    * **Set IP**: **sudo ifconfig eth0 192.168.1.10 netmask 255.255.255.0** to assign a static IP.
        
3. **iptables**
    
    * **sudo iptables -L**: Lists all firewall rules, quickly shows allowed/blocked traffic.
        
    * **Block IP**: **sudo iptables -A INPUT -s 192.168.1.5 -j DROP** blocks incoming traffic from a specific IP.
        
    * **Open Port 80**: **sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT** allows HTTP traffic for web servers.
        

---

### **Interview Questions**

1. **What does netstat -an** do?  
    Lists all active connections, great for spotting open or unused ports.
    
2. **Why use ifconfig** to bring down an interface?  
    To reset its connection or apply new configurations easily.
    
3. **How to allow SSH in iptables**?  
    **sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT** opens port 22 for secure access.