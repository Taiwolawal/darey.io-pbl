## BUILDING ELASTIC KUBERNETES SERVICE (EKS) WITH TERRAFORM

EKS is a managed Kubernetes service that makes it easy for you to run Kubernetes on AWS without needing to install, operate, and maintain your own Kubernetes control plane. In this projetc we will be using Terraform to set it up and futher in the project we will use Helm, which is a package manager for Kubernetes is used to deploy multiple applications.


## Building EKS with Terraform

We need to setup some terraform resources 
* Creating AWS S3 bucket from a CLI to store the Terraform state: 
```
ws s3api create-bucket --bucket eks-terraform-buck --region us-east-1
````
* Create a new directory eks for the Terraform file
* Create a file backend.tf for the remote state in S3 to save the infrastructure state and Dynamodb for lock 
```
resource "aws_s3_bucket" "terraform-state" {
  bucket = "eks-terraform-prj24"

  # force_destroy = true

}

resource "aws_s3_bucket" "terraform-state" {
  bucket = "eks-terraform-prj24"

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
  name         = "state-locking"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"
  attribute {
    name = "LockID"
    type = "S"
  }
}

terraform {
  backend "s3" {
    bucket         = "eks-terraform-prj24"
    key            = "global/s3/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "state-locking"
    encrypt        = true
  }
}
```

* Create network.tf file and provision Elastic IP for Nat Gateway, VPC, Private and public subnets.
```
# reserve Elastic IP to be used in our NAT gateway
resource "aws_eip" "nat_gw_elastic_ip" {
  vpc = true

  tags = {
    Name            = "${var.cluster_name}-nat-eip"
    iac_environment = var.iac_environment_tag
  }
}

module "vpc" {
  source = "terraform-aws-modules/vpc/aws"

  name = "${var.name_prefix}-vpc"
  cidr = var.main_network_block
  azs  = data.aws_availability_zones.available_azs.names

  private_subnets = [
    # this loop will create a one-line list as ["10.0.0.0/20", "10.0.16.0/20", "10.0.32.0/20", ...]
    # with a length depending on how many Zones are available
    for zone_id in data.aws_availability_zones.available_azs.zone_ids :
    cidrsubnet(var.main_network_block, var.subnet_prefix_extension, tonumber(substr(zone_id, length(zone_id) - 1, 1)) - 1)
  ]

  public_subnets = [
    for zone_id in data.aws_availability_zones.available_azs.zone_ids :
    cidrsubnet(var.main_network_block, var.subnet_prefix_extension, tonumber(substr(zone_id, length(zone_id) - 1, 1)) + var.zone_offset - 1)
  ]
  enable_nat_gateway     = true
  single_nat_gateway     = true
  one_nat_gateway_per_az = false
  enable_dns_hostnames   = true
  reuse_nat_ips          = true
  external_nat_ip_ids    = [aws_eip.nat_gw_elastic_ip.id]

  # Add VPC/Subnet tags required by EKS
  tags = {
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
    iac_environment                             = var.iac_environment_tag
  }
  public_subnet_tags = {
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
    "kubernetes.io/role/elb"                    = "1"
    iac_environment                             = var.iac_environment_tag
  }
  private_subnet_tags = {
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
    "kubernetes.io/role/internal-elb"           = "1"
    iac_environment                             = var.iac_environment_tag
  }
```

Note: The tags added to the subnets is very important. The Kubernetes Cloud Controller Manager (cloud-controller-manager) and AWS Load Balancer Controller (aws-load-balancer-controller) needs to identify the cluster’s. To do that, it querries the cluster’s subnets by using the tags as a filter.

For public and private subnets that use load balancer resources: each subnet must be tagged
```
Key: kubernetes.io/cluster/cluster-name
Value: shared
```
For private subnets that use internal load balancer resources: each subnet must be tagged
```
Key: kubernetes.io/role/internal-elb
Value: 1
```
For public subnets that use internal load balancer resources: each subnet must be tagged
```
Key: kubernetes.io/role/elb
Value: 1
```
* Create a file – variables.tf
```
# create some variables
variable "cluster_name" {
  type        = string
  description = "EKS cluster name."
}

variable "iac_environment_tag" {
  type        = string
  description = "AWS tag to indicate environment name of each infrastructure object."
}

variable "name_prefix" {
  type        = string
  description = "Prefix to be used on each infrastructure object Name created in AWS."
}

variable "main_network_block" {
  type        = string
  description = "Base CIDR block to be used in our VPC."
}

variable "subnet_prefix_extension" {
  type        = number
  description = "CIDR block bits extension to calculate CIDR blocks of each subnetwork."
}

variable "zone_offset" {
  type        = number
  description = "CIDR block bits extension offset to calculate Public subnets, avoiding collisions with Private subnets."
}

variable "admin_users" {
  type        = list(string)
  description = "List of Kubernetes admins."
}

variable "developer_users" {
  type        = list(string)
  description = "List of Kubernetes developers."
}

variable "asg_instance_types" {
  description = "List of EC2 instance machine types to be used in EKS."
}

variable "autoscaling_minimum_size_by_az" {
  type        = number
  description = "Minimum number of EC2 instances to autoscale our EKS cluster on each AZ."
}

variable "autoscaling_maximum_size_by_az" {
  type        = number
  description = "Maximum number of EC2 instances to autoscale our EKS cluster on each AZ."
}

variable "admin_users" {
  type        = list(string)
  description = "List of Kubernetes admins."
}
variable "developer_users" {
  type        = list(string)
  description = "List of Kubernetes developers."
}
variable "asg_instance_types" {
  description = "List of EC2 instance machine types to be used in EKS."
}
variable "autoscaling_minimum_size_by_az" {
  type        = number
  description = "Minimum number of EC2 instances to autoscale our EKS cluster on each AZ."
}
variable "autoscaling_maximum_size_by_az" {
  type        = number
  description = "Maximum number of EC2 instances to autoscale our EKS cluster on each AZ."
}
````

* Create data.tf file
```
# get all available AZs in our region
data "aws_availability_zones" "available_azs" {
state = "available"
}
data "aws_caller_identity" "current" {} # used for accesing Account ID and ARN

# get EKS cluster info to configure Kubernetes and Helm providers
data "aws_eks_cluster" "cluster" {
  name = module.eks_cluster.cluster_id
}
data "aws_eks_cluster_auth" "cluster" {
  name = module.eks_cluster.cluster_id
}
```

* Create file – eks.tf and provision EKS cluster
```
module "eks_cluster" {
  source                          = "terraform-aws-modules/eks/aws"
  version                         = "~> 18.0"
  cluster_name                    = var.cluster_name
  cluster_version                 = "1.22"
  vpc_id                          = module.vpc.vpc_id
  subnet_ids                      = module.vpc.private_subnets
  cluster_endpoint_private_access = true
  cluster_endpoint_public_access  = true

  # Self Managed Node Group(s)
  self_managed_node_group_defaults = {
    instance_type                          = var.asg_instance_types[0]
    update_launch_template_default_version = true
  }
  self_managed_node_groups = local.self_managed_node_groups

  # aws-auth configmap
  create_aws_auth_configmap = true
  manage_aws_auth_configmap = true
  aws_auth_users            = concat(local.admin_user_map_users, local.developer_user_map_users)
  tags = {
    Environment = "prod"
    Terraform   = "true"
  }
}
```

* Create locals.tf file to create local variables
```
# render Admin & Developer users list with the structure required by EKS module
locals {
  admin_user_map_users = [
    for admin_user in var.admin_users :
    {
      userarn  = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${admin_user}"
      username = admin_user
      groups   = ["system:masters"]
    }
  ]
  developer_user_map_users = [
    for developer_user in var.developer_users :
    {
      userarn  = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${developer_user}"
      username = developer_user
      groups   = ["${var.name_prefix}-developers"]
    }
  ]

  self_managed_node_groups = {
    worker_group1 = {
      name = "${var.cluster_name}-wg"

      min_size      = var.autoscaling_minimum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      desired_size  = var.autoscaling_minimum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      max_size      = var.autoscaling_maximum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      instance_type = var.asg_instance_types[0].instance_type

      bootstrap_extra_args = "--kubelet-extra-args '--node-labels=node.kubernetes.io/lifecycle=spot'"

      block_device_mappings = {
        xvda = {
          device_name = "/dev/xvda"
          ebs = {
            delete_on_termination = true
            encrypted             = false
            volume_size           = 10
            volume_type           = "gp2"
          }
        }
      }

      use_mixed_instances_policy = true
      mixed_instances_policy = {
        instances_distribution = {
          spot_instance_pools = 4
        }

        override = var.asg_instance_types
      }
    }
  }
}
```

* Create a file, variables.tfvars to set values for variables.
```
cluster_name            = "tooling-app-eks"
iac_environment_tag     = "development"
name_prefix             = "taiwo-io-eks"
main_network_block      = "10.0.0.0/16"
subnet_prefix_extension = 4
zone_offset             = 8

# Ensure that these users already exist in AWS IAM. Another approach is that you can introduce an iam.tf file to manage users separately, get the data source and interpolate their ARN.
admin_users                    = ["taiwo", "esther"]
developer_users                = ["bolaji", "sheriff"]
asg_instance_types             = [{ instance_type = "t3.small" }, { instance_type = "t2.small" }, ]
autoscaling_minimum_size_by_az = 1
autoscaling_maximum_size_by_az = 5
```

* Create provider.tf file to show the provider we are are working with 
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

provider "aws" {
  region = "us-west-1"
}

# get EKS authentication for being able to manage k8s objects from terraform
provider "kubernetes" {
  host                   = data.aws_eks_cluster.cluster.endpoint
  cluster_ca_certificate = base64decode(data.aws_eks_cluster.cluster.certificate_authority.0.data)
  token                  = data.aws_eks_cluster_auth.cluster.token
}
```

* Run ```terraform init``` command
![image](https://user-images.githubusercontent.com/50557587/180241574-cc8e5f6c-cb0c-41ea-aed4-a00a09bdba7d.png)
![image](https://user-images.githubusercontent.com/50557587/180241649-e4afecf8-aea1-4cd6-b582-ccc4197fd852.png)

* Create kubeconfig file using awscli: ```aws eks update-kubecofig --name tooling-app-eks --region us-west-1 --kubeconfig kubeconfig```

* Run terraform apply to create our cluster
![image](https://user-images.githubusercontent.com/50557587/180242396-9c3340f5-3d0f-4536-a425-b5c3daaeace8.png)
![image](https://user-images.githubusercontent.com/50557587/180242594-393d9107-d638-4c0b-a794-bb0370104cc0.png)


## Installing Helm From Script
* Fetching the script: ```$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 ```
* Changing the permission of the script: ```$ chmod 700 get_helm.sh```
* Executing the script: ```$ ./get_helm.sh```

![image](https://user-images.githubusercontent.com/50557587/180243867-127fd2bd-a1df-40b5-973a-5a69dc55fd01.png)

## Deploying Jenkins With Helm
* Adding the Jenkins' repository to helm so it can be easily downloaded and deployed: ```$ helm repo add jenkins https://charts.jenkins.io```
* Updating helm repo: ```$ helm repo update```
* Installing the chart: ```$ helm install myjenkins jenkins/jenkins --kubeconfig kubeconfig```
* The jenkins chart was giving error so I ran this command ```curl -L https://git.io/get_helm.sh | bash -s -- --version v3.8.2```.
*  Installation of the kubectl was giving error also, so I had to troubleshoot and found out that the version of the kubectl 1.24.0 installed was having issues so I had to revert  to 1.23.6
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.23.6/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```
* To avoid typing --kubeconfig kubeconfig anytime want to run kubectl command

Link to get krew installation https://krew.sigs.k8s.io/docs/user-guide/setup/install/
```
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)
```

![image](https://user-images.githubusercontent.com/50557587/180186584-ad5f2327-3a32-443f-8fd6-3c8ac9236392.png)

```
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
kubectl krew install konfig
kubectl konfig import --save kubeconfig
```
* To show all the contexts for the clusters configured in my kubeconfig. ```$ kubectl config get-contexts```

![image](https://user-images.githubusercontent.com/50557587/180189234-2c128873-0ff9-4d40-86ff-63383029f667.png)

* Now we can run kubectl to interact with the cluster
* Change file permissions on the configuration file ```chmod 600 ~/.kube/config```.

![image](https://user-images.githubusercontent.com/50557587/180189399-ad9dca37-9e71-4083-acf6-79f05a5f5ebe.png)

![image](https://user-images.githubusercontent.com/50557587/180192232-d7e52a67-1c9a-4040-827c-32e4d15a5358.png)

![image](https://user-images.githubusercontent.com/50557587/180192798-39b06312-a7ae-4145-b808-4a745ad819f2.png)

![image](https://user-images.githubusercontent.com/50557587/180193000-6f95bcda-bc18-499a-a3e1-829f06c22c46.png)

## Deploying Hashicorp Vault With Helm
* Adding the Hashicorp's repository to helm: ```helm repo add hashicorp https://helm.releases.hashicorp.com```
* Updating helm repo: ```helm repo update```.
* Installing the chart: ```helm install vault hashicorp/vault```

![image](https://user-images.githubusercontent.com/50557587/180263563-76b74857-8bb2-418d-b92f-8c5be8bcca58.png)






