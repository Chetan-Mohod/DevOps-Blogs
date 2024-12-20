---
title: "Terraform Best Practices: How to Leverage Locals, Count, and For_each"
datePublished: Fri Dec 20 2024 19:06:55 GMT+0000 (Coordinated Universal Time)
cuid: cm4x4glct000209mjf6byfbp6
slug: terraform-best-practices-how-to-leverage-locals-count-and-foreach
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734721539030/4087e2e7-eceb-48e1-a217-e9ad3ba5393c.png
tags: ec2, aws, foreach, devops, blockchain, terraform, instance, locals-block

---

> Prerequisite: ***To gain a better understanding, it is advisable to read this blog first:*** [***Click here***](https://hashnode.com/post/cm4qj10kx000e09l4fqhhfss3) ***before continuing with this one.***
> 
> GitHub: Refer this [repository](https://github.com/Chetan-Mohod/terraform_work/tree/main).

---

Ever felt like Terraform configurations could be simpler or more flexible? Dive into the magic of the `locals` block and the meta-arguments `count` and `for_each`!

---

## Locals Block:

I want to create two instances with Terraform but their name should be different. For this purpose we need to create Locals block. Add below code in our `variables.tf` file.

*Note: All the previous code has been explained in earlier sections of the Terraform documentation, so please refer to those sections and the GitHub repository.*

```bash
#varibales.tf
locals {
  instances = {
    #Key   : #Value
    "key"  : "my_instance_1"
    "key2" : "my_instacne_2"
  }
}
```

Now, if we want to use these local instance variables in our instance tags, how should we do it?

Solution: We will iterate over our locals and fetch their values one by one for the tags.

Apply these changes into our `ec2.tf` file within the existing code:

```bash
#Exisiting ec2.tf file

resource "aws_instance" "my_instance" {
  count           = var.aws_instance_count  #Meta Argument
#--------------------------------------------------------------#
  for_each        = local.instances          #Meta Argument
#--------------------------------------------------------------#
  ami             = data.aws_ami.os_image.id
  instance_type   = var.ec2_instance_type
  security_groups = [aws_security_group.my_sg.name] #Interpolation
  key_name        = aws_key_pair.my-key.key_name    #Interpolation
  root_block_device {
    volume_size = var.aws_root_volume_size
    volume_type = "gp3"
  }
  tags = {
#--------------------------------------------------------------#
    Name = each.value
#--------------------------------------------------------------#
  }
}
```

In this code, `each.value` represents the value from the `instances` map during each iteration of `for_each`. For example, if the map has `{"key" = "my_instance_1"}`, then `each.value` will be `"my_instance_1"`. It's used here to dynamically set the `Name` tag for each AWS instance.

Lets try to plan the changes using `terraform plan`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734711021418/36d0042d-8d34-439a-bf53-ff88c3803a3e.png align="center")

The error happens because you can’t use both `count` and `for_each` in the same resource because they’ are alternatives for creating multiple resources.

<mark>IMP Note:</mark> Using `count`, all instance names will be the same. However, by using `for_each`, we can assign different names to each instance and there values will be configurable.

We will comment out the `count` for now.

```bash
#ec2.tf
resource "aws_instance" "my_instance" {
  #count           = var.aws_instance_count  #Meta Argument
  for_each        = local.instances          #Meta Argument
  ami             = data.aws_ami.os_image.id
  instance_type   = var.ec2_instance_type
  security_groups = [aws_security_group.my_sg.name] #Interpolation
  key_name        = aws_key_pair.my-key.key_name    #Interpolation
  root_block_device {
    volume_size = var.aws_root_volume_size
    volume_type = "gp3"
  }
  tags = {
    Name = each.value
  }
}
```

Now we will run terraform plan again.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734716608257/d0769a52-f786-4630-95c5-c3b592124262.png align="center")

You will notice "Plan 5 to add," indicating that the instances are being created, but there is an error in `outputs.tf`.

We need to retrieve the values from the `outputs` block using `for_each`. Let's explore how to achieve this.

\[ [Documentation1](https://stackoverflow.com/questions/64989080/modules-output-from-for-each) , [Documentation2](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each) \]

```bash
#outputs.tf

output "ec2_public_ip" {
  value = {
    for key, instance in aws_instance.my_instance : key => instance.public_ip
  }
}
```

This is the `for` loop:

* `aws_`[`instance.my`](http://instance.my)`_instance` refers to the EC2 instances created using `for_each`.
    
* `key` is the identifier for each EC2 instance (like a name or tag).
    
* `instance` contains the full details of the EC2 instance (like IP address, state, etc.).
    
* `key => instance.public_ip` creates a key-value pair: the `key` is the instance identifier, and the `value` is the `public_ip` of that instance.
    

Run `terraform plan` again:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734718052917/5395a4c9-9675-439f-996d-3ac1c58c3323.png align="center")

Run `terraform apply`, and you will see that it displays the public IPs of our instances as well.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734718487782/04e59afb-0cf4-4003-a802-800f504e8840.png align="center")

You can observe in the AWS console that our two instances are running with different names.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734720136280/f53ddd69-ff97-4c25-a013-a337d06773c8.png align="center")

This is how you can use locals block in Terraform.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).