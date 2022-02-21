# Automate Infrastructure With IAC Using Terraform Part 3.

This is the continuation of Project 17. In this project we are going to refactor the project with modules, change our backend to S3 and apply DynamoDB. 

We are going to change our backend to S3, by default we were using our local storage. Our terraform.tfstate file is stored locally on our machine, and assuming we have different engineers working on this project we need to ensure that everyone working on this project all have the same file across board, so as a result we store the file on cloud for easy access to other engineers and ensuring they all files are same.

We are going to refactor our code to use modules. Create a directory called modules, and inside the it, create directories named VPC, Security, EFS, RDS, ALB, Autoscaling, Compute. This directory created, we are going to copy files related to the named directory e.g For the VPC directory, the files needed to setup our VPC are internet-gateway, Nat-gateway, Routes etc

With module, it is reuseable by different engineers , the only thing to do is just to tweak some parameters input to your desire taste. Ensure all the arguments in the module are declared as a variable, just to ensure we do not hardcode values.

Also create variables.tf file in all the created directories, so the content of the directory will have a variables.tf file and other files related to the name of the directories.

The content of the modules directory will be like the screenshot below:  
![image](https://user-images.githubusercontent.com/50557587/154669777-289fe6f2-a2cd-46ba-9934-69dddacc3029.png)

Ensure to run terraform init anytime time you create modules.

To make use of the content in the modules, you need to use the keyword module and the directory name you are referencing, and state the source where the directory is, just like what you have in the screenshot below:  
![image](https://user-images.githubusercontent.com/50557587/154671456-4a3bc570-5840-4683-8770-6a3794072d95.png)


![image](https://user-images.githubusercontent.com/50557587/154808135-2a580979-2c52-4124-966e-3d59a536d322.png)

We launch terraform apply, all the resource should work fine with all the the necessary tweak done
![image](https://user-images.githubusercontent.com/50557587/154808155-f539eea4-96dc-4af2-88c0-ab577f42376e.png)
![image](https://user-images.githubusercontent.com/50557587/154808171-6a00922b-16f2-4250-94f2-f6de596befd7.png)
![image](https://user-images.githubusercontent.com/50557587/154808236-04abcfd1-df07-4eaf-967b-60f77965bed5.png)
![image](https://user-images.githubusercontent.com/50557587/154808261-ae628ed7-60aa-4eeb-bfbd-4f1d9ea411c6.png)
![image](https://user-images.githubusercontent.com/50557587/154808276-b5b2e32b-0168-4f4e-afba-430b81599789.png)
![image](https://user-images.githubusercontent.com/50557587/154808301-93ee7245-789c-4b32-a955-1997af51e5dd.png)
![image](https://user-images.githubusercontent.com/50557587/154808315-bf746317-091c-4e1a-9d24-bbedee5386a8.png)

Now we are going to introduce Backend on S3.

So far, we have been using the default backend, which is the local backend – it requires no configuration, and the states file is stored locally. This mode can be suitable for learning purposes, but it is not a robust solution, so it is better to store it in some more reliable and durable storage.

The second problem with storing this file locally is that, in a team of multiple DevOps engineers, other engineers will not have access to a state file stored locally on your computer

To solve this, we will need to configure a backend where the state file can be accessed remotely other DevOps team members. There are plenty of different standard backends supported by Terraform that you can choose from. Since we are already using AWS – we can choose an S3 bucket as a backend.

Another useful option that is supported by S3 backend is State Locking – it is used to lock your state for all operations that could write state. This prevents others from acquiring the lock and potentially corrupting your state. State Locking feature for S3 backend is optional and requires another AWS service – DynamoDB.

Create an S3 bucket to store Terraform state file with a unique name 
```
resource "aws_s3_bucket" "terraform-state" {
  bucket = "pbl18"

  # force_destroy = true

}

resource "aws_s3_bucket_versioning" "version" {
  bucket = aws_s3_bucket.terraform-state.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "first" {
  bucket = aws_s3_bucket.terraform-state.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

```

Create a DynamoDB table to handle locks and perform consistency checks. In previous projects, locks were handled with a local file as shown in terraform.tfstate.lock.info. Since we now have a team mindset, causing us to configure S3 as our backend to store state file, we will do the same to handle locking. Therefore, with a cloud storage database like DynamoDB, anyone running Terraform against the same infrastructure can use a central location to control a situation where Terraform is running at the same time from multiple different people.
```
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-locks"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"
  attribute {
    name = "LockID"
    type = "S"
  }
}
```
Terraform expects that both S3 bucket and DynamoDB resources are already created before we configure the backend. So, let us run terraform apply to provision resources.

Create file and name is backend.tf and reference the bucket name, then configure the backend. We need to ensure that we run `terraform init` before we can make use of the backend. The purpose of this is to migrate our statefile and other files from local storage to S3.
```
terraform {
  backend "s3" {
    bucket         = "pbl18"
    key            = "global/s3/terraform.tfstate"
    region         = "us-east-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```


![image](https://user-images.githubusercontent.com/50557587/154809933-811b1dd4-86d4-4aab-a141-13c3c8fec1d8.png)

```
terraform {
  backend "s3" {
    bucket         = "pbl18"
    key            = "global/s3/terraform.tfstate"
    region         = "us-east-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

![image](https://user-images.githubusercontent.com/50557587/154810019-d49926f0-36c6-48d9-b438-a28b9ec5da7c.png)
![image](https://user-images.githubusercontent.com/50557587/154810075-6a7c7588-736b-4893-b6cf-e361586cf97d.png)

When we run terraform plan or apply, a new lock ID would be generated while running the plan. The DynamoDB is handling the lockID, it will be present until the plan or apply finishes. 
![image](https://user-images.githubusercontent.com/50557587/154810223-d2b23d46-8734-4c1c-9f49-7e4994f1332a.png)


We have successfully refactored our project to use modules, introduce backend as S3 and also used DynamoDB to store our state lockfiles.

 We should run terraform destroy, but before we run it, we should take note that terraform will destroy the S3 bucket and if it destroy it , terraform will go into a undefined state and delete the files you have there so avoid this by commenting out your S3 backend and run `terraform init -migrate-state` at that point it will copy your statefile back from S3 to your local storage.  
![image](https://user-images.githubusercontent.com/50557587/154810615-6268e3d2-0d59-48eb-9dc7-a4288f653820.png)
 
 Note that to use S3 as a backend, you need to enable versioning on the bucket.


