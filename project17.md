# Automate Infrastructure With IAC Using Terraform Part 2.

This is the continuation of Project 16, where we have already created VPC and  public subnets.

We continue by creating private subnet and entrying the code below in main.tf
```
resource "aws_subnet" "private" {
  vpc_id     = aws_vpc.main.id
  count      = var.preferred_number_of_private_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_private_subnets
  cidr_block = cidrsubnet(var.vpc_cidr, 8, count.index + 2)

  tags = merge(
    var.tags,
    {
      Name = format("%s-PRIVATE-SUBNET-%s", var.name, count.index)
    },
  )
}
````
Enter the values below in the variables.tf and terraform.tfvars respectively. 
```
variable "preferred_number_of_private_subnets" {
  type        = number
  description = "Number of private subnets"
}

preferred_number_of_private_subnets = 4

````
