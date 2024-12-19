---
title: "Terraform State Locking on AWS: Hands-On with S3 and DynamoDB"
datePublished: Thu Dec 19 2024 18:27:03 GMT+0000 (Coordinated Universal Time)
cuid: cm4vnlgi2000d0ajs9dhz5668
slug: terraform-state-locking-on-aws-hands-on-with-s3-and-dynamodb
tags: devops, terraform

---

> Prerequisite: ***To gain a better understanding, it is advisable to read this blog first:*** [***Click here***](https://hashnode.com/post/cm4u84uul000609me0t537c5j) ***before continuing with this one.***

## State Management Hands-on:

I am writing this blog to enhance my practical experience with the concepts discussed in my previous blog on Terraform State Management. Let's begin.

* Create a separate folder in VS Code with name terraform-backends. Here you will create a backend Infrastructure
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734547758201/815f19dc-b46c-45ab-b1ac-6f8c35407fc9.png align="center")

* You just need to create Dynamo DB table and S3 bucket in Infrastructure.
    
* You can copy the `terraform.tf` file from the `terraform-practice` folder and add it directly to our `terraform.tf` file into `terraform-backends` folder. This setup is intended for different backend infrastructure using S3 and Dynamo DB.
    

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

* Create a `variables.tf` file and add an entry for `aws_region`:
    

```bash
variable "aws_region" {
    description = "AWS Region"
    default     = "eu-west-1"
}
```

* Let's create an AWS S3 Bucket and DynamoDB in the `backend_infra.tf` file:
    
* What is DynamoDB? It is a NoSQL database, which stands for Not Only SQL. This means you can perform queries without needing to create tables first. In MySQL, you must define a schema for the table, including the table name and column names, as it is a relational database. However, in NoSQL databases, there is no predefined schema.
    

```bash
resource "aws_s3_bucket" "my_bucket" {
  bucket = var.aws_s3_bucket
  tags = {
    Name = var.aws_s3_bucket
  }
}

resource "aws_dynamodb_table" "my_dynamo_table" {
  name = var.aws_dynamodb_table
  #billing_mode   = "PROVISIONED"  #It is for constant billing
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
  tags = {
    Name = var.aws_dynamodb_table
  }
}
```

* Refer Terraform [Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/dynamodb_table) to create Dynamo DB.
    
* We will add variables in out `variable.tf` file:
    

```bash
variable "aws_region" {
  description = "AWS Region"
  default     = "eu-west-1"
}

variable "aws_s3_bucket" {
  description = "AWS Backend Bucket name"
  default     = "state-locking"
}

variable "aws_dynamodb_table" {
  description = "AWS Backend Dynamo Table name"
  default     = "acrobat-state-table"
}
```

* Explanation of Dynamo DB code:
    
    * **resource "aws\_dynamodb\_table" "my\_dynamo\_table" {**  
        Declares a DynamoDB table resource.
        
    * **name = var.aws\_dynamodb\_table**  
        Sets the table name dynamically using a variable.
        
    * **billing\_mode = "PAY\_PER\_REQUEST"**  
        Enables billing only for what you use.
        
    * **hash\_key = "LockID"**  
        Sets "LockID" as the table's primary key.
        
    * **attribute {**  
        Defines the attributes for the table. It is like a column name.
        
    * **name = "LockID"**  
        Declares "LockID" as the attribute name.
        
    * **type = "S"**  
        Specifies the attribute type as a string.
        
* Now we will execute our code.
    

```bash
terraform init
terraform plan
terraform apply -auto-approve  #It is used to approve without saying yes
```

* Now you can see our S3 bucket & Dynamo DB Table has been created on AWS.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734585178352/45bc61fe-ee65-4c81-840b-c026cbc0a6fd.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734585303754/ee24b422-b993-480b-918d-b9476557ea5f.png align="center")

* If you navigate to the Explore item in DynamoDB, you will notice it is empty. This is because we have not yet utilized the acrobat-state-table and state-locking.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734585515677/170f7a56-c19f-4fc7-bba6-982de3a2c120.png align="center")

* Now we will go to our `terraform-practice` folder and open `terraform.tfstate` file.
    
* To manage how the Terraform state file is maintained, we need to modify the "`terraform.tf`" file to operate at the Terraform level. This is necessary to attach a remote backend, ensuring that your tfstate file is stored in a remote S3 bucket.
    
* Add the following code to the `terraform.tf` file in our `terraform-practice` folder:
    

```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  backend "s3" {
    bucket = "state-locking"   #bucket name
    key = "terraform.tfstate"  #State file name
    region = "eu-west-1"
    dynamodb_table = "acrobat-state-table"
  }
}

provider "aws" {
  region = var.aws_region
}
```

* **Explanation of backend code:**
    
    * **backend "s3"**  
        Configures S3 as the Terraform backend.
        
    * **bucket = "state-locking" #bucket name**  
        Specifies the S3 bucket to store the state.
        
    * **key = "terraform.tfstate" #State file name**  
        Sets the file name for storing Terraform state.
        
    * **region = "eu-west-1"**  
        Defines the AWS region for the bucket.
        
    * **dynamodb\_table = "acrobat-state-table"**  
        Uses DynamoDB for state locking to prevent conflicts.
        
* Now open the terminal and go to `terraform-practice` folder.
    
* Whenever you add a provider or make changes at the Terraform level, it is important to run `terraform init` first.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734628737040/29923cb4-8eb0-4340-9c34-cb35e8f418aa.png align="center")

* As shown in the image below, our S3 bucket is currently empty.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734628795424/19201a18-432d-473a-b0a2-a9c8959eef9c.png align="center")

* Once we confirm, the `terraform.tfstate` file will be backed up in the S3 bucket, as shown in the image below.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734628925977/c6719551-2255-42c6-b6b2-234551dc9914.png align="center")

* If you delete `terraform.tfstate` and `terraform.tfstate.backup` and then run `terraform state list`, you will notice that the state is no longer available locally. However, the state still appears in the terminal because it is stored in the remote backend (S3 Bucket).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734629115828/99b8c60f-7dab-49b0-a6d0-92df7f6c917e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734629474532/d6303165-7dab-422f-b2c8-ae93db22fcaf.png align="center")

* Now lets increase our `instance count = 2` & apply the changes and then see how state locking works.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734629665751/d99a8ee4-a500-4be0-a367-55432b8936c0.png align="center")

You will observe the message "Acquiring state lock". Next, navigate to the `acrobat-state-table`, click on "view tables details", and then refresh the page. You should see that two items are returned.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734629812775/a522d87e-66ee-42d8-a3f3-6e9e7bd951d8.png align="center")

* You can click on "edit" to view detailed information, such as the user's identity and other relevant details.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734630093177/a30e5ee4-7f33-4772-af3a-adb53e8ae361.png align="center")

* Now, open another terminal tab while keeping the original tab with the `terraform apply` command open. In the new tab, increase the instance count to 10 and run `terraform apply`. Observe the results:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734630968209/27b3e8bf-559f-491f-bba3-32b2f1a24f50.png align="center")

* You can observe that it prevents multiple users from accessing the `terraform.tfstate` file simultaneously.
    
* Now you can see state lock has been removed and only digest will be there.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734631163660/c7f642b2-bb91-4514-87a8-a760101089c4.png align="center")

* Now, we can modify the instance state of the two servers we created using either the AWS console (start, stop) or Terraform. Let's see how to achieve this using Terraform. Refer [documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ec2_instance_state).
    

```bash
resource "aws_ec2_instance_state" "test" {
  instance_id = aws_instance.test.id
  state       = "stopped"
}
```

This is how you can use Terraform State Locking.

---

## **Happy Learning :)**

**Chetan Mohod ✨**

For more DevOps updates, you can follow me on [**LinkedIn**](https://www.linkedin.com/in/chetanmohod/).