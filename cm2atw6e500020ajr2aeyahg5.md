---
title: "Top 20 Essential Linux Commands Every DevOps Engineer Should Master"
datePublished: Tue Oct 15 2024 19:20:46 GMT+0000 (Coordinated Universal Time)
cuid: cm2atw6e500020ajr2aeyahg5
slug: top-20-essential-linux-commands-every-devops-engineer-should-master
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1729019917063/7953bafc-7997-4a07-a81f-308ae1b4f148.jpeg

---

* **File and Directory Management**
    

1. **ls**: Lists directory contents of the directory.  
    ls -l
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729016934042/7a8b2c4d-a10e-47ca-839b-0ab90aed2315.png align="center")
    
    *If you run “****ls -l****” it will display detailed information about files and directories, including permissions, ownership, and modification dates. It is called longlist.*
    
2. **cd**: Changes the current directory.  
    cd /var/log
    
    *Example*: Navigate to the /var/log directory to check system logs.
    
3. **mkdir**: Creates a new directory.  
    mkdir /backup
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729017097652/06f10c37-e52e-4971-b6a1-44f6492e7d42.png align="center")
    
    *Example*: Create a directory named /backup for storing backup files.
    
    *Note: Anything which starts with* ***d*** *is directory and* ***\-*** *is file.*
    
4. **rm**: Removes files or directories.  
    rm-rf /tmp/\*
    
    *Example*: Forcefully remove all files and directories within /tmp.
    

* **File Content Manipulation**
    

5. **cat**: Concatenates and displays file content.
    
    *Example*: Display the contents of the user.txt file.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729017872826/9ac429bb-a17a-4ff5-806c-17ef237fd7f2.png align="center")
    
6. **grep**: Searches for patterns in files.
    
    *Example*: Search “John” in the user.txt file.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729017798591/10704dbc-7517-42fa-a9d7-7edceda33fa4.png align="center")
    
7. **awk**: Pattern scanning and processing language.  
    *Example:*
    
    * Imagine you have a file named user.txt with the following content:
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729017376324/814543f5-ebbd-4bc8-99cf-50e1fc3115a6.png align="center")
    
    * And you want to print the age and name from this file then we can use awk command:
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729017589570/ac5fda72-dc89-43df-8d1d-5b7667af0cd1.png align="center")
        

* **Process Management**
    

8. **ps**: Displays information about active processes.  
    ps aux
    
    *Example*: Show detailed information about all running processes.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729017936786/ff28b8e4-ace3-4447-a8ea-1ce58b9be745.png align="center")
    
9. **top**: Displays real-time system summary and process information.  
    top
    
    *Example*: Monitor system performance and resource usage in real-time.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729018324449/bd8ca26f-8cd2-418a-b784-73b30d01c5eb.png align="center")
    
10. **kill**: Terminates processes.  
    kill-9 1234
    
    *Example*: Forcefully terminate the process with PID 1234.
    
    *Note: A PID is or Process ID, which is a unique identifier assigned to each running process in Linux.*
    

* **Networking**
    

11. **ifconfig**: Configures network interfaces.  
    ifconfig
    
    *Example*: Display configuration details of the network interface.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729019797685/2a27ff22-a299-457e-9d39-0c43761f59d2.png align="center")
    
12. **netstat**: Displays network connections, routing tables, and interface statistics.  
    netstat -a
    
    *Example*: It will list all network connections. Similar to -a you can use multiple options with netstat like (-r, -at, -tuln)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729018851802/b2d2b3aa-4000-4c29-be3c-d0f978e98b8f.png align="center")
    
13. **curl**: Transfers data from or to a server.  
    curl -I [https://google.com](https://google.com)
    
    *Example*: Fetch HTTP headers from a website.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729019020242/ba390feb-5929-434a-8d83-a9e108cc7b6a.png align="center")
    
14. **ssh:** Securely connects to a remote server.
    
    **ssh user@hostname**
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729015485594/9cb1eb0f-6708-4a7c-8a8f-fd75508776a5.png align="center")
    
    *Example*: Log into a remote server as the specified user. This command is crucial for remote management and administration of servers, allowing you to execute commands and manage systems from anywhere.
    

**System Monitoring and Management**

15. **df**: Reports file system disk space usage.  
    df-h
    
    *Example*: Display disk space usage in a human-readable format.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729019074512/1f6b8731-45dc-4e8e-aad5-edcec891784c.png align="center")
    
16. **du**: Estimates file space usage.  
    du -sh /home/ubuntu
    
    *Example*: Show the total disk usage of the /home/ubuntu directory.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729019204891/54bdab1b-cd46-4ecc-8e62-570bc2004452.png align="center")
    
    *Here du stands for disk usage, -s means summarize and -h means human readable.*
    
17. **uptime**: Tells how long the system has been running.  
    uptime  
    *Example*: Display the system uptime and load averages.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729019310454/807e49c4-a920-4f17-997b-62bf6fe97303.png align="center")
    

**Automation and Scripting**

18. **crontab**: Schedules periodic tasks.  
    crontab -e
    
    *Example*: Edit the cron jobs for the current user. In below example it will take backup of the path every minute with the help of cronjob.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729019376660/609b92f7-0775-4395-85a6-36dda61b2edb.png align="center")
    
    crontab -l
    
    It will list the crons for current user.
    
19. **bash**: Executes shell scripts.  
    bash script.sh
    
    *Example*: Run a shell script named script.sh.
    

**Version Control**

20. **git**: Version control system.  
    git clone https://github.com/user/repo.git
    
    *Example*: Clone a repository from GitHub.
    

**Additional Important Command (Most used command)**

21. **sudo**: Super User DO
    
    **sudo** is a group that has privileges of a root user. It lets you perform tasks requiring special permissions, like an all-access pass for your computer.
    

Example:

1. To install any package:
    
    sudo apt-get nginx
    
2. To remove/uninstall any package:
    
    sudo apt remove nginx
    
3. To restart any service:
    
    sudo systemctl restart nginx
    
4. To add user and password:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729016905453/e5002020-9e01-47fd-b580-7ca5e3487ad5.png align="center")
    

If you want to streamline your daily tasks and boost your productivity, mastering these 20 Linux commands is a game-changer. Dive into these commands and take your DevOps skills to the next level!

Keep Learning!