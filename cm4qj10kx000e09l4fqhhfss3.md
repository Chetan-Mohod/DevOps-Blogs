---
title: "Mastering Terraform: Streamline EC2 Management with Variables, Outputs, Data Blocks & Real-World Fixes"
datePublished: Mon Dec 16 2024 04:20:20 GMT+0000 (Coordinated Universal Time)
cuid: cm4qj10kx000e09l4fqhhfss3
slug: mastering-terraform-streamline-ec2-management-with-variables-outputs-data-blocks-real-world-fixes
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734322709043/3c4e5864-f42a-4323-b0e7-e17296c3779a.png
tags: variables, aws, devops, terraform, 90daysofdevops, 90daysofdevops-chanllenge, 90daysofdevops-devops-projectdevelopment-nonitbackground-github-docker-cloudplatforms-ec2-aws-elasticbeanstalk-lambdafunctions-devopspipelines-terraform-jenkins-docker-devsecops-scm-git-gitlab-bitbucket-buildtools-griddle-maven-ant-msbuild-monitoringtools-prometheus-grafana-ansible-ai-chatgpt-valueaddition-realworldproblems, 90daysofdevopschallenge, terraform-data-block, terraform-outputs-block, variables-block

---

> Prerequisite: ***To gain a better understanding, it is advisable to read this blog first: Click here*** [***blog1***](https://devops-concepts-by-chetan.hashnode.dev/terraform-made-easy-learn-from-scratch-with-diagrams-tricks-and-real-examples) ***&*** [***blog2***](https://devops-concepts-by-chetan.hashnode.dev/terraform-your-way-to-aws-mastery-build-s3-iam-and-ec2-like-a-pro) ***before continuing with this one.***

When you have set up an EC2 Instance using Terraform in a single `main.tf` file and need to change the instance type or volume size, it's generally not recommended to make direct changes. In IT, there's a saying: "If something is working fine in production, don't touch it." This means we should avoid repeatedly committing changes.

## What is the Variables Block?

So, what is the solution? You can create a new file named `variable.tf` to manage variables. This allows you to easily change values across all your Terraform code.

**Please read** [**blog2**](https://devops-concepts-by-chetan.hashnode.dev/terraform-your-way-to-aws-mastery-build-s3-iam-and-ec2-like-a-pro) **before continuing. In that blog, I created an EC2 instance that we will reference here. Reading it first will help you avoid feeling overwhelmed.**

Steps to use Variable.tf:

1. Create an variable.tf file and add below code in it.
    

```bash
variable "aws_region" {
  description = "This is the region specified for AWS Resources"
  default = "eu-west-1"
}

variable "aws_ec2_ami_id" {
  description = "This is AMI ID for Ubuntu Instance"
  default = "ami-0e9085e60087ce171"
}

variable "ec2_instance_type" {
  description = "This is the Instance type for EC2"
  default = "t2.micro"
}

variable "aws_ec2_instance_name" {
  description = "This is the name given to EC2 instance"
  default = "Chetan-Auto-Server"
}

variable "aws_root_volume_size" {
  description = "This is the size of root volume for aws EC2"
  default = 10
}
```

The term "parameter" re fers to the "variable name" and is specific to Terraform.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734029053534/8e665e18-1af9-4b3b-9146-42d4cd6684de.png align="center")

2. Edit terraform.tf and ec2.tf by using interpolation for variables.
    

```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```

```bash
resource "aws_key_pair" "my-key" {
  key_name   = "acrobat-terraform-key"
  public_key = file("acrobat-terraform-key.pub")
}

resource "aws_default_vpc" "default" {

}

resource "aws_security_group" "my_sg" {
  name        = "Z plus security"
  description = "This is a security grp created by Terraform"
  vpc_id      = aws_default_vpc.default.id #This is called interpolatiion
  ingress {                                #Inbound Rule
    description = "Allow access to SSH port 22"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] #Anywhere around the IP access grant
  }
  ingress { #Inbound Rule
    description = "Allow access to SSH port 80"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress { #Outbound Rule
    description = "allow all outgoing traffice"
    to_port     = 0
    from_port   = 0
    protocol    = "-1" #Why -1? Outgoing traffic to everytype HTTP, HTTPS, TCP, etc
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = {
    Name = "Z plus security"
  }
}

resource "aws_instance" "my_instance" {
  ami             = var.aws_ec2_ami_id
  instance_type   = var.ec2_instance_type
  security_groups = [aws_security_group.my_sg.name] #Interpolation
  key_name        = aws_key_pair.my-key.key_name    #Interpolation
  root_block_device {
    volume_size = var.aws_root_volume_size
    volume_type = "gp3"
  }
  tags = {
    Name = var.aws_ec2_instance_name
  }
}
```

3. Now we will do terraform plan.
    
4. The advantage of using `variable.tf` is that it centralizes changes, allowing you to update this file instead of modifying your entire codebase. In production environments, adjustments are made primarily in the `variable.tf` files.
    

---

## Meta Keywords:

If you want to create an EC2 instance and if you add `count = 3` into the resource block of an instance then it will create **three** of instances.

```bash
#variable.tf

variable "aws_instance_count" {
  description = "The count of EC2 instance needed"
  default     = 3
}
```

```bash
#ec2.tf

resource "aws_instance" "my_instance" {
  count           = var.aws_instance_count
  ami             = var.aws_ec2_ami_id
  instance_type   = var.ec2_instance_type
  security_groups = [aws_security_group.my_sg.name] #Interpolation
  key_name        = aws_key_pair.my-key.key_name    #Interpolation
  root_block_device {
    volume_size = var.aws_root_volume_size
    volume_type = "gp3"
  }
  tags = {
    Name = var.aws_ec2_instance_name
  }
}
```

---

## Data Block & filters:

Suppose we have added `ami id` in our code 4-5 years back and now latest `ami id` has been launched for that specific instance, so it is very serious thing. Here comes data block into the picture.

What data block do?

It will fetch data for terraform, for example we will fetch AWS ami id and we can store it into the variable as shown below:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734031904598/c19a53ad-f26e-45bb-b054-06865ef0f6e4.png align="center")

In the data block, we can store the `aws ami id` as demonstrated in the code below:

```bash
#Add this in the top of your main.tf. Here we will add in our ec2.tf file

data "aws_ami" "os_image" {
  most_recent = true
  filter {
    name = "state" #In this case, it’s filtering AMIs based on their state
    values = ["available"] # Ensures the AMI is in the "available" state
  }
  filter {
    name   = "name"
    values = ["ubuntu/images/*"] #Matches AMIs whose names start with "ubuntu/images/"
  }
}
```

It retrieves the latest OS image/AMI ID for the instance. For more information, you can refer to the Data Source - [aws\_ami\_ids](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ami_ids), which fetches the latest AMI IDs for Ubuntu that are available in the specified state.

Change resource block for your instance accordingly:

```bash
resource "aws_instance" "my_instance" {
  count           = var.aws_instance_count
  ami             = data.aws_ami.os_image.id
  instance_type   = var.ec2_instance_type
  security_groups = [aws_security_group.my_sg.name] #Interpolation
  key_name        = aws_key_pair.my-key.key_name    #Interpolation
  root_block_device {
    volume_size = var.aws_root_volume_size
    volume_type = "gp3"
  }
  tags = {
    Name = var.aws_ec2_instance_name
  }
}
```

By utilizing the **Data block** and **filter**, you can retrieve the latest AMI ID for your infrastructure in Terraform.

Now try to do terraform plan and if you get below error

It is asking for owner id or image id

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734074856998/6e2ed835-9e29-491d-b3a0-dc5243f2293d.png align="center")

So lets copy the owner id from AMI and follow below steps as mentioned into screenshot.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734074517436/c9155e7b-a4ab-4da1-bb3a-896d5b673987.png align="center")

Then add this AMI id into our data block:

```bash
#ec2.tf

data "aws_ami" "os_image" {
  owners = [ 679593333241 ]
  most_recent = true
  filter {
    name   = "state"
    values = ["available"]
  }
  filter {
    name   = "name"
    values = ["ubuntu/images/*"]
  }
}
```

Now run terraform plan again, and see the results.

---

## Outputs Block

I have an AWS EC2 instance, and I need to access port 80.

How can you access it?

First, you need to know its IP address.

Let's create an `outputs.tf` file:

```bash
output "ec2_public_ip" {
  value = aws_instance.my_instance.public_ip
}


output "ec2_private_ip" {
  value = aws_instance.my_instance.private_ip
}
```

The output always includes a value. When you run `terraform plan`, it will display attributes in the terminal. Copy the **public\_ip/private\_ip** attribute from there.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734088072272/027318f7-92f3-481d-b140-787dfccb85de.png align="center")

Now if we run terraform plan, let’s see what happens:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734088418557/e5251e19-1da9-4261-910d-aa6fa3174266.png align="center")

If you have added a count in ec2.tf, you need to include \[`count.index`\] in the output. Using indexes helps to assign the correct IPs to the respective instances, as demonstrated below.

The `[ * ]` is called the splat operator. Think of it as a way to grab all the private IP addresses if you've created multiple EC2 instances with `my_instance`. Instead of getting just one private IP, it collects all of them into a list so you can see every private IP for all the instances you’ve launched.

```bash
#output.tf

output "ec2_public_ip" {
  value = aws_instance.my_instance[*].public_ip
}

output "ec2_private_ip" {
  value = aws_instance.my_instance[*].private_ip
}
```

Run `terraform plan` again, and you will be able to obtain your public and private IP addresses after applying the changes.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734110123958/df9a89c2-61f4-4c19-b4ac-b1a26e3c1201.png align="left")

```bash
terraform apply
```

Now you can view your AMI ID:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734193556861/4f8b5c40-4d11-4ed3-b447-b706931d985d.png align="left")

Use this IP to navigate to the AMIs section on the AWS portal. Apply the relevant filters and select any Owner ID.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734193823571/7e3302c4-14e7-46c0-a755-0790894f794d.png align="center")

Now include this owner ID in our code as demonstrated below, and then execute `terraform apply`:

```bash
data "aws_ami" "os_image" {
 owners = [ 679593333241 ]
  most_recent = true
  filter {
    name   = "state"
    values = ["available"]
  }
filter {
    name = "name"
    values = ["ubuntu/images/hvm-ssd/*amd64*"]
  }
```

Now during terraform apply it is throwing this error, so visit that website and Accept Term & Conditions.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734318597617/9eac5a6e-58eb-4a0a-be4d-a632308515ea.png align="center")

We can also use official images, which is straightforward. Navigate to Launch Instance to obtain the AMI ID. However, I am demonstrating the practical aspect of DevOps. After copying the AMI ID from Launch Instance, go to AMIs and search for that ID to use it in our code. Then, execute `terraform apply`, and it will function correctly.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734318966021/8c75db10-354f-4fbe-88ce-ebad57e9590a.png align="center")

To update your setup, replace your existing code with the following code:  

```bash
data "aws_ami" "os_image" {
 owners = ["099720109477"]
  most_recent = true
  filter {
    name   = "state"
    values = ["available"]
  }
  filter {
    name = "name"
    values = ["ubuntu/images/hvm-ssd-gp3/*amd64*"]
  }
}
```

Run `terraform apply`, and your code should now function correctly.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734319348454/cc0a2087-50ba-427c-84a1-f01e2b09b5bf.png align="center")

In this real-world Terraform scenario, you encountered an issue where your data might not be selecting the correct AMI image.

This is how your EC2 instance appears in a production environment, where you have the owner set, key, owner's architecture, instance count, and other configurations.

---

## Most Asked Interview Question:

1. **count vs for\_each**
    

—&gt; In Terraform, creating multiple resources is a common task. But how do you choose between **count** and **for\_each**?

Here’s a quick breakdown:

* **count**: If you know exactly how many resources you need, like creating 5 EC2 instances, use **count**. It's straightforward and you can access each instance with `count.index`. Example:
    
    ```bash
    hclCopy coderesource "aws_instance" "example" {
      count = 5
      ami   = "ami-12345"
      instance_type = "t2.micro"
    }
    ```
    
* **for\_each**: When the number of resources isn’t fixed or you need to use a collection (like a list or map), go with **for\_each**. It’s perfect for situations where each resource might have a different configuration. Example:
    
    ```bash
    hclCopy coderesource "aws_instance" "example" {
      for_each = toset(["web", "db", "cache"])
      ami      = "ami-12345"
      instance_type = "t2.micro"
      tags = {
        Name = each.value
      }
    }
    ```
    

Choose wisely depending on your scenario!

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).