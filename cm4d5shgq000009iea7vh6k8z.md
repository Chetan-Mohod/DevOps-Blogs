---
title: "Terraform Made Easy: Learn from Scratch with Diagrams, Tricks, and Real Examples!"
datePublished: Fri Dec 06 2024 19:48:46 GMT+0000 (Coordinated Universal Time)
cuid: cm4d5shgq000009iea7vh6k8z
slug: terraform-made-easy-learn-from-scratch-with-diagrams-tricks-and-real-examples
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733513519032/64de92fe-781c-465a-b95d-e13cc50b5669.png
tags: devops, hashnode, terraform, cloudcomputing, iac, devops-journey, 90daysofdevops, cloud-infrastructure, 90daysofdevops-chanllenge, infrastructureascode, hcl, terraweekchallenge

---

> To understand what Terraform is and why it is used, you can read this [blog](https://hashnode.com/post/cm4cvrsyq000009jpb8p417x5). If you are already familiar with Terraform, then let's proceed with this blog.

### Steps for installing Terraform:

1. First, we will configure an EC2 instance on AWS, allocating a storage volume of 15 GB. Please refer to this blog for guidance on creating an [EC2 Instance](https://hashnode.com/edit/cm3df80je000609jm20ih7lge) on AWS.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733499669819/90e64c8a-9632-4723-b321-812574326a52.png align="center")

2. To establish an SSH connection with this instance, use the terminal:
    
3. To install Terraform, please visit the official [Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) documentation.
    
4. We will install all the necessary components on our Ubuntu Linux Server.
    
    Please follow the steps below:
    
    ```bash
    sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
    ```
    

* Install the HashiCorp [GPG key](https://apt.releases.hashicorp.com/gpg).
    
    * The GPG key is used to ensure the authenticity of the Terraform download. It is necessary to verify that the software is genuine and has not been tampered with.
        

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

* Verify the key's fingerprint.
    

```bash
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
```

* Now, we will add our key to the system so that the HashiCorp list is included in the sources list.
    

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

* Download the package information from HashiCorp.
    

```bash
sudo apt update
```

* Now Install Terraform from the new repository.
    

```bash
sudo apt-get install terraform

terraform --version
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733502138175/1a6e6be4-0290-46a2-8a30-12caaa269a6d.png align="left")

You can now see the version of Terraform that has been downloaded and installed on your system.

---

### What is HCL?

* Terraform is a tool developed by HashiCorp, written in HashiCorp Configuration Language (HCL).
    
* We will learn how to write in HashiCorp Configuration Language (HCL), which is JSON like object.
    
* The extension for Terraform files is (.tf).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733505993662/bd492fd8-c46f-4271-b895-3b513cf44441.png align="center")

<mark>block</mark> —&gt; resource, output, variable, data, etc.

*Ex.* ***resource***\*&lt;block&gt; &lt;parameter&gt;{\*

*args*

*}*

<mark>parameter </mark> —&gt; resource instance and resource name. The Resource Name is used by Terraform for its understanding and is not the same as our EC2 instance name. The instance name is specified in the argument section.

*Ex.* ***resource***\*&lt;block&gt;\* ***aws\_s3\_bucket***\*&lt;parameter&gt; my\_s3\_bucket&lt;resource name&gt;{\*

*args*

*}*

To identify a Terraform resource, the name assigned to it is referred to as the resource name.

<mark>arguments(configurations) </mark> —&gt;

For example, when launching a new EC2 instance, a form appears for its creation. We fill out this form with details known as arguments or configurations, such as instance name, instance type, storage, keys, and more.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733507178074/11b1d825-544b-43e8-a5ea-e782e76210a5.png align="center")

Lets take one example:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733507621428/89b27c97-3115-4329-b490-863e12f53593.png align="center")

### Terraform file creation:

* Create a Terraform file on our server:
    

```bash
 mkdir terraform-practice

cd terraform-practice

vim main.tf #(Terraform File)
```

* Write code for file creation:
    

```bash
resource "local_file" "my_file" {
  filename = "Devops.txt"
  content  = "This is Terraform auto generated file"
}
```

* Terraform operates in several steps:
    
    * First, we create a `.tf` file.
        
    * Next, we <mark>initialize</mark> the file. During initialization, Terraform scans the file, installs the necessary components, and creates the Terraform object.
        
    * Then, we <mark>plan</mark> the object. This step shows how the object will appear when executed.
        
    * The plan provides a preview, which is a rough output that doesn't yet exist but demonstrates how it will look. This is similar to a dry run.
        
    * If the preview is satisfactory, you can <mark>apply</mark> these changes to finalize them.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733508960390/7e4da49b-ba98-449c-b960-a5848c42c2fa.png align="center")

Steps to implement this file:

1. **Terraform init**: It will initialize Terraform in this particular directory.
    
    Terraform init will initialize the folder with Terraform and install whatever the provider are required in your main.tf file.
    

```bash
terraform init
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733510672866/dd8ecaad-297c-4001-9ed8-62a300bbf5e8.png align="center")

* **What is local here?**
    
    * The term following **hashicorp/"\_\_\_\_\_\_"** is referred to as providers.
        
    * When we specify the resource type "**local\_file**," it indicates the **local** provider being used. For example, in "**aws\_s3\_bucket**," the provider is **aws**.
        
* As soon as we run `terraform init` it will create `.terraform`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733510977004/e2a05a7a-3c40-4d27-9b5a-a3752a4e1a67.png align="center")

* If you look inside the .terraform directory, you will find all the providers listed there.
    

2. **Terraform plan:** It provides a preview, which is a rough output that doesn't yet exist but demonstrates how it will look. This is similar to a dry run.
    

```bash
terraform plan
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733511554287/74990a64-3725-45e6-ac4f-fbdde8b456b2.png align="center")

We have specified arguments such as content and filename. The elements we define ourselves are known as <mark>arguments</mark>, while the elements that are ( known after apply ) are referred to as <mark>attributes</mark>.

3. **Terraform apply:** This command is used to implement the changes.
    

```bash
terraform apply
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733511854249/a709f592-2dc4-4288-b08a-7996b42765a4.png align="center")

4. Validations:
    

```bash
terraform state list #Terraform known file name

ls   #File name in filesystem
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733512459554/1a7efd69-5e86-4f1e-814a-373d7855e5f6.png align="left")

`local_file.my_file`: This is the Terraform reference name for the `Devops.txt` file that we created.

5. **Terraform Destroy:** This command allows us to remove our resources.
    

```bash
terraform destroy
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733513222076/159e7c71-8b84-4adb-ada7-d13733050a29.png align="center")

Now, you can see that the Devops.txt file no longer exists after we run the destroy command.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733513248265/ad077bd6-6477-4eb8-b6df-62f13e4fecf6.png align="left")

In Terraform, you just need to understand **blocks, parameters,** and **arguments**. That’s it.

### Interview Question:

* **Difference between arguments and attributes:**
    
    \==&gt; Arguments are specified in the Terraform configuration file within a resource block. They define the desired state of the resource. Attributes, on the other hand, are details about the resource that become known after the changes are applied.
    

### Happy Learning :)

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [LinkedIn](https://www.linkedin.com/in/chetanmohod/).