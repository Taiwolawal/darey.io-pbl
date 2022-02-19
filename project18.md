# Automate Infrastructure With IAC Using Terraform Part 3.

This is the continuation of Project 17. In this project we are going to modularize our project by creating different folder e.g VPC, EFS, RDS, ALB.

We are going to create a folder named modules, and inside the folder, create folders named VPC, Security, EFS, RDS, ALB, Autoscaling, Compute. This folders created, we are going to copy files related to the name of the folder e.g For the VPC folders, the files needed to setup our VPC are internet-gateway, Nat-gateway, Routes etc

With a module, it is reuseable by different engineers , the only thing to do is just to tweak some parameters input to your desire taste.

Also create variables.tf file in all the created folders, so the content of the folders will be variables.tf file and other files related to the name of the folders.

The content of the modules folder will be like the screenshot below:  
![image](https://user-images.githubusercontent.com/50557587/154669777-289fe6f2-a2cd-46ba-9934-69dddacc3029.png)

To make use of the content in the modules, you need to use the keyword module and the folder name you are referencing, and state the source where the folder is, just like what you have in the screenshot below:  
![image](https://user-images.githubusercontent.com/50557587/154671456-4a3bc570-5840-4683-8770-6a3794072d95.png)


![image](https://user-images.githubusercontent.com/50557587/154808135-2a580979-2c52-4124-966e-3d59a536d322.png)
![image](https://user-images.githubusercontent.com/50557587/154808155-f539eea4-96dc-4af2-88c0-ab577f42376e.png)
![image](https://user-images.githubusercontent.com/50557587/154808171-6a00922b-16f2-4250-94f2-f6de596befd7.png)
![image](https://user-images.githubusercontent.com/50557587/154808236-04abcfd1-df07-4eaf-967b-60f77965bed5.png)
![image](https://user-images.githubusercontent.com/50557587/154808261-ae628ed7-60aa-4eeb-bfbd-4f1d9ea411c6.png)
![image](https://user-images.githubusercontent.com/50557587/154808276-b5b2e32b-0168-4f4e-afba-430b81599789.png)
![image](https://user-images.githubusercontent.com/50557587/154808301-93ee7245-789c-4b32-a955-1997af51e5dd.png)
![image](https://user-images.githubusercontent.com/50557587/154808315-bf746317-091c-4e1a-9d24-bbedee5386a8.png)

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

