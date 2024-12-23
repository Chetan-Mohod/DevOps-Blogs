---
title: "AWS IAM: A Step-by-Step Guide to Secure Your AWS Account and Manage Resources"
datePublished: Mon Dec 23 2024 15:48:57 GMT+0000 (Coordinated Universal Time)
cuid: cm517pjkz000g09l3ax8cearf
slug: aws-iam-a-step-by-step-guide-to-secure-your-aws-account-and-manage-resources
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734968865902/6ab3d2ff-b1ad-4b9f-8444-706b4aeffdb3.png
tags: ec2, aws, security, iam, aws-iam, iammfaaccess-key-idsecret-access-key, iam-identities, iam-role-in-aws, iam-policies

---

When you access your AWS account, you can view your account details and the services you have disabled. If you grant access to your account to someone else and they enable services without disabling them afterward, it can lead to high costs and potential security and compliance issues. Therefore, it is important to understand a service called Identity Access Management (IAM).

Go to the AWS Console, search for IAM, and click on IAM.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734692340263/4a930092-5dfe-44db-b461-e440b8c641af.png align="center")

* Here you can find security recommendations.
    
    * There are two security concerns:
        
        * **Root user has MFA:**
            
            * We can use an MFA Authenticator app on both a laptop and a phone.
                
        * **Root user has no active access keys:**
            
            * We can access the account through a password or the CLI. In the CLI, instead of a password, we use tokens and keys. For security reasons, the root user should not have an access key because if it falls into the wrong hands, it could lead to significant issues.
                

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734789307325/9b1b1161-b109-4421-88f4-002f6d293214.png align="center")

* In IAM, you can manage multiple resources. Let's start with Users.
    

### #Users:

* To follow these security recommendations, we can enable our MFA and remove the access keys.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734789136473/76463a84-b09f-4580-b7d1-0a161442b404.png align="center")

### #Identity Providers:

* If you want to provide access to your AWS account to JIRA, GitHub, GitLab then you can add them as a **Identity Provider**.
    
* You can also grant access to your AWS account to another user's AWS account.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734789665401/21d06d3d-79f1-4dea-811d-da3a8b7dd898.png align="center")

### #Policies:

* It's a written document that details what access users have. For example, if your user has access to create and read EC2 instances then the user can only perform the tasks they have access to in the policy.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734789999921/787581e1-31cf-4dae-b9f0-3668746a1dc1.png align="center")

* Scenario: If you are an admin and a new employee joins who is a fresher, and you want to grant them read/write access, you need to go to IAM, create a user, and then attach the necessary access to the EC2 instance. So, the user can only access the EC2 instance and change their password upon login, but nothing more.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734790485787/dc7d6baa-a678-4a97-b47e-884bc1a1d4b2.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734790572213/6011afac-9714-48bf-8e54-88d23bc5fb9e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734790659797/7509668e-e1db-4187-88c4-68a49044fb3a.png align="center")

* Now the user can log in to the console, change their password on the first login, and then create EC2 instances, but they cannot do anything beyond this. This is known as Policy.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734790759977/6ae68e2f-cfb5-492d-83d9-06c338b915a1.png align="center")

---

## Create a user with CLI access

To create a user with access to the Command Line Interface (CLI), follow these steps:

1. Click on "Create a User":
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734791561532/08d18c20-007f-427b-aa6a-9ba5259c6c57.png align="center")

2. Add a username and click "Next." Since console access is not required, you can skip the checkbox for console access.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734791642229/d2667f29-e582-44c7-b747-9c3d25e80934.png align="center")

3. Now you can see multiple options here:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734791742460/fe00dfbe-89ec-4397-a6db-f7210693e4c9.png align="center")

* First, we will examine groups:
    
    * Here, we can assign group permissions to the user. The permissions that the group has will automatically apply to our user.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734792519032/ffea5b22-fbd6-47fa-9fc0-2c88b9cf518f.png align="center")

* The best approach is to create a group, attach the necessary policies to it, and then add the user to that group. As shown in above image.
    
* Create a group and add EC2 Instance full access.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734792702698/5f6efff1-251d-4d14-8104-c836e66bbe27.png align="center")

* Once group created add any user to that group as shown below:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734792945697/268735c7-0fc1-4c5c-980a-9eff998466ee.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734792976691/a80749b7-b05b-4e01-96ea-b5ff84b2af89.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734793001716/c3bcb369-6774-451b-a167-2041d48cf6a0.png align="center")

* Our user has access to the EC2 Instance, but we do not want to grant them direct access to the database. However, since the user is a developer, they need to perform insert and delete operations within the instance.
    
* To address this, we will assign a Role to our EC2 instance, enabling it to interact with the database. This way, the user does not have direct database access, but they can work on the EC2 instance, which has the necessary Service Roles to interact with the database. Thus, we are granting permission to the EC2 instance to communicate with the database.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734794409607/92e3c6b3-a2b4-4a74-b814-5d42a1f861d4.png align="center")

* Now click on "Create Role."
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734796221019/36911ddc-6f9b-4558-9fd3-8b3423d3c139.png align="center")

* Select "AWS Service" and choose "EC2" in the "Use case" section, then click "Next."
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734796256286/a8cc143d-0463-4e59-8f74-18f6736ed6c4.png align="center")

* Select RDS permissions:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734796369393/6d24d124-28dc-4cfc-95ae-f86b64fd9f6a.png align="center")

* Enter a Role Name and click on Create Role:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734796427858/982e9a44-f18b-4d77-a7c5-a46cac674005.png align="center")

4. To assign the created role to your EC2 instance, follow these steps:
    

* Navigate to the EC2 Console by searching for "EC2" in the search box, which will redirect you to the EC2 Console.
    
* Locate your Instance and click on “Actions” and follow the below steps:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734796638689/c5a06f0b-d5f4-44f8-b362-e88bbeb9dd45.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734797891765/f108c638-9ca0-4e05-84b3-9a47a711fa2c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734798085370/92961097-fc0f-4205-bc78-6ccf1b4e736d.png align="center")

This is how you can interact with RDB via EC2.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).