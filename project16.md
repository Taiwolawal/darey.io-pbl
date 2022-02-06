# Automate Infrastructure With IAC Using Terraform Part 1.

After we have built AWS infrastructure for 2 websites manually, it is time to automate the process using Terraform.

![image](https://user-images.githubusercontent.com/50557587/152675210-6ffeadaf-dc76-4302-aaeb-bdad320537e7.png)

For easier authentication configuration – use AWS CLI with aws configure command.  
![a](https://user-images.githubusercontent.com/50557587/152675315-147e5489-e796-4ce2-b22e-cd5b9dad607a.PNG)

Create an S3 bucket to store Terraform state file. We will use this bucket from Project-17 onwards.  
![f](https://user-images.githubusercontent.com/50557587/152675329-040545c9-b0a6-4816-a802-56f533eec0d3.PNG)
![g](https://user-images.githubusercontent.com/50557587/152675331-795f98e3-1996-46a3-857e-2bb0d3d80162.PNG)

Create a folder called PBL, and create a file in the folder, name it main.tf.   

Add AWS as a provider, and a resource to create a VPC in the main.tf file

Provider block informs Terraform that we intend to build infrastructure within AWS.

Add the following code in the main.tf file.  
```
provider "aws" {
  region = "eu-central-1"
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = "172.16.0.0/16"
  enable_dns_support             = "true"
  enable_dns_hostnames           = "true"
  enable_classiclink             = "false"
  enable_classiclink_dns_support = "false"
}

```
The next thing we need to do, is to download necessary plugins for Terraform to work. These plugins are used by providers and provisioners. At this stage, we only have provider in our main.tf file. So, Terraform will just download plugin for AWS provider.

Lets accomplish this with terraform init command as seen in the below demonstration.  
![image](https://user-images.githubusercontent.com/50557587/152676100-49d09d35-5e8d-4806-8ecc-e48137f03efd.png)

Run terraform plan, it will display actions the terraform will take.

*Observations:*
* A new file is created terraform.tfstate This is how Terraform keeps itself up to date with the exact state of the infrastructure. It reads this file to know what already exists, what should be added, or destroyed based on the entire terraform code that is being developed.
* If you also observed closely, you would realise that another file gets created during planning and apply. But this file gets deleted immediately. terraform.tfstate.lock.info This is what Terraform uses to track, who is running its code against the infrastructure at any point in time. This is very important for teams working on the same Terraform repository at the same time. The lock prevents a user from executing Terraform configuration against the same infrastructure when another user is doing the same – it allows to avoid duplicates and conflicts.

According to our architectural design, we require 6 subnets:
* 2 public subnets
* 2 private subnets for webservers
* 2 private subnets for data layer

Add below configuration to the main.tf file:
```
# Create public subnets1
    resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1a"

}

# Create public subnet2
    resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1b"
}
```
We are creating 2 subnets, therefore declaring 2 resource blocks – one for each of the subnets.

We are using the vpc_id argument to interpolate the value of the VPC id by setting it to aws_vpc.main.id. This way, Terraform knows inside which VPC to create the subnet.

*Observations:*
* Hard coded values: Remember our best practice hint from the beginning? Both the availability_zone and cidr_block arguments are hard coded. We should always endeavour to make our work dynamic.
* Multiple Resource Blocks: Notice that we have declared multiple resource blocks for each subnet in the code. This is bad coding practice. We need to create a single resource block that can dynamically create resources without specifying multiple blocks. Imagine if we wanted to create 10 subnets, our code would look very clumsy. So, we need to optimize this by introducing a count argument.

Let us improve our code by refactoring it.

We will create different files namely: variables.tf, terraform.tfvars

The variables.tf will contain the content below:
```
variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = null
}
```

The terraform.tfvars file will contain the content below:





![b](https://user-images.githubusercontent.com/50557587/152675319-4b680446-4c71-4b03-be1b-d47851ad32b3.PNG)
![c](https://user-images.githubusercontent.com/50557587/152675324-8149a54c-924a-404e-aa13-568b9027b9be.PNG)
![d](https://user-images.githubusercontent.com/50557587/152675326-f5b8d82f-2ec9-4f91-85b7-17da62af3313.PNG)
![e](https://user-images.githubusercontent.com/50557587/152675327-380d1649-5703-4d3c-b985-23f1ed689402.PNG)

![h](https://user-images.githubusercontent.com/50557587/152675332-e1e20668-51b7-439d-8d9f-d745bb247cf9.PNG)
![i](https://user-images.githubusercontent.com/50557587/152675336-dc948203-8b45-4057-bf12-24d5c9202ff4.PNG)
