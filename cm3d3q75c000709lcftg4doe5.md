---
title: "Lost Access to Your AWS EC2 Instance? Here’s How to Get Back In Without the Key"
datePublished: Mon Nov 11 2024 14:11:18 GMT+0000 (Coordinated Universal Time)
cuid: cm3d3q75c000709lcftg4doe5
slug: lost-access-to-your-aws-ec2-instance-heres-how-to-get-back-in-without-the-key
tags: aws, aws-ec2, private-key, awsaccesskey

---

Losing your AWS EC2 key can be a real trouble. You’re locked out of your own instance, and AWS doesn’t exactly make it easy to get back in. There are few ways to get access again. I’ll walk you through what you can try, and toss in a few things you can do to keep this from happening again.

So, **Why’s This Key So Important?** A quick rundown: EC2 key pairs work with two parts:

The Public Key is stored by AWS and acts like a virtual lock. The Private Key (**.pem** file) is your copy the only way in. If you misplace your private key, AWS doesn’t have a spare for you. That’s why if you lose it, you’ll need one of the following recovery options to get back in.

**First Things First:** Are There Any Other Access Points? Before we get into the recovery process, pause for a second. Are you already connected? Or do you have an open SSH session somewhere? If so, you can quickly add a new key without the whole workaround.

If not, no worries. Here’s where the methods kick in.

**Method 1:** Use AWS Systems Manager (SSM) If SSM is set up, it’s like a secret backdoor. SSM lets you connect to EC2 instances without needing the usual SSH keys. Here’s the lowdown:

Make Sure **SSM** Is Available: SSM requires your instance to have internet access or an SSM endpoint, plus an IAM role attached (with AmazonEC2RoleforSSM permissions).

Connect Using **Session Manager**: Head to the Systems Manager console, find Session Manager, and select your instance. This opens a browser-based terminal session.

From here, you can update the **~/.ssh/authorized\_keys** file with a new SSH key, so you’re good to go for future logins.

Method 2: Attach the Root Volume to Another Instance If SSM isn’t set up, you can still access your instance through the root volume by attaching it to a temporary instance. Here’s a walkthrough:

Stop the Instance: In the EC2 console, select Stop Instance. Don’t terminate it—just stop.

Detach the Root Volume: Under Elastic Block Store (EBS), find the root volume of your instance and detach it.

Attach the **Volume to a Temporary Instance:** Fire up a new instance in the same availability zone and attach the detached volume to this instance as a secondary volume (it might be labeled /dev/sdf).

Add a **New Key:** SSH into the temporary instance, mount the attached volume, and find the authorized\_keys file. Add a new key here, and save.

**Reattach and Restart:** Detach the volume from the temporary instance, then reattach it as the root volume of your original instance. Restart, and you’re back in with the new key!

A Few Quick Tips to Avoid This Next Time Set Up SSM on All Instances: Having SSM enabled from the start means you have an alternative way to access the instance if you ever misplace the key.

Keep Keys in **Secure Storage:** Think password manager or encrypted storage. Anything that’s secure but handy.

Use **IAM Roles** for Applications: Where possible, go with IAM roles over SSH keys for applications running on your EC2 instance, reducing the chance you’ll even need SSH access.