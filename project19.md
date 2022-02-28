# Automate Infrastructure With IaC using Terraform. Part 4 - Terraform Cloud.

In thsi project we will be  using Packer to build our images, and Ansible to configure the infrastructure, so for that we are going to make few changes to our our existing respository from Project 18.

The files that would be addedd are;

* AMI: for building packer images
* Ansible: for Ansible scripts to configure the infrastructure

### Action Plan for project 19

- Build images using packer
- confirm the AMIs in the console
- update terrafrom script with new ami IDs generated from packer build
- create terraform cloud account and backend
- run terraform script
- update ansible script with values from teraform output
     - RDS endpoints for wordpress and tooling
     - Database name, password and username for wordpress and tooling
     - Access point ID for wordpress and tooling
     - Internal load balancee DNS for nginx reverse proxy

- run ansible script
- check the website

Install packer on your machine and clone `https://github.com/Taiwolawal/packer-ami-pbl-19.git` which contains the AMI installation and input data required in each AMI created.

Run packer build for each of the files required and confirm if the AMI's were created.  
![image](https://user-images.githubusercontent.com/50557587/155509676-82e4aad6-6b19-47d9-bb71-1f227f08a5ab.png)
![image](https://user-images.githubusercontent.com/50557587/155512423-f372c7bf-2053-4812-918f-70e6d72174b7.png)

Update the new AMI's ID from the packer build  in the terraform script  
![image](https://user-images.githubusercontent.com/50557587/155521464-066a9d0d-9eda-467e-bf70-a1e99250b1c8.png) 
![image](https://user-images.githubusercontent.com/50557587/155999037-ec8d8e18-40e6-4556-805f-38340cc7e9d7.png)

Create  terraform cloud account and backend. Connect your github repo (containing the terraform script) with the cloud account and this will create a workspace on the account, which is where all terraform plan, apply, destroy and other commands will be executed. The workspace ensures that any changes made in the repo will be noticed by terraform cloud and it will run the code which is more like you running terraform plan and if you want to apply it, you will run it on the terraform cloud UI.     
![image](https://user-images.githubusercontent.com/50557587/155510755-80ab1621-68d9-423e-9850-ff0f89cc991c.png)
![image](https://user-images.githubusercontent.com/50557587/155512221-456c3a43-b98f-4ce4-aff1-119b91f9c2e9.png)
![image](https://user-images.githubusercontent.com/50557587/155710300-2272fd7f-85cd-4627-9f4c-4ab33c8a5ee7.png)

On your  workspace, the states files created when an apply is made on the terraform script is kept on the account compared to having it locally and the backend.

Run apply on the terraform script via the account UI.

After the apply is run, ensure that all resources are created as expected.

SSH into the Bastion instance and clone` https://github.com/Taiwolawal/ansible-deploy-pbl-19.git` which contains Ansible scripts which will be used to configure the infrastructure as required.

To ensure the Ansible file can get all the required information from our AWS account such as our instance IP addresses, tags for each instances, we need to run `aws configure` and enter all required credentials.

Ensure that we have ssh-agent enabled on our bastion instance, so that we can easily SSH into Nginx and Webservers.

Update the ansible script with values such as:
- RDS endpoints for wordpress and tooling
- Database name, password and username for wordpress and tooling
- Access point ID for wordpress and tooling
- Internal load balancee DNS for nginx reverse proxy 

![image](https://user-images.githubusercontent.com/50557587/155975629-75d006c5-5bd6-4a21-85bb-2864653024aa.png)
![image](https://user-images.githubusercontent.com/50557587/155976474-9f3bb595-ebb2-4252-a06d-d1aaff601558.png)
![image](https://user-images.githubusercontent.com/50557587/155978317-22e58321-7a78-454e-94ea-e10ff2569af5.png)

To confirm if ansible have access to all the resources run `ansible-inventory -i inventory/aws_ec2.yml --graph`.     
![image](https://user-images.githubusercontent.com/50557587/155758102-b092d397-a3d6-4086-9bcb-b5b336dcae69.png)  

Run `ansible-playbook -i inventory/aws_ec2.yml playbooks/site.yml` to configure the infrastructure.     
![image](https://user-images.githubusercontent.com/50557587/156018678-44ebcd83-9b09-4884-be11-b40d900f1249.png)


Confirm if all configuration were all implemented.



![image](https://user-images.githubusercontent.com/50557587/155731918-6a4fef75-e81a-42dd-a1c0-1a027de2b117.png)

![image](https://user-images.githubusercontent.com/50557587/155748349-43063718-684c-4ffd-9a66-a8e67e6004ec.png)
![image](https://user-images.githubusercontent.com/50557587/155748432-726a1bc5-e187-4c03-84db-6d1806b7628b.png)
![image](https://user-images.githubusercontent.com/50557587/155748507-e0725b04-9d8c-4683-af2d-d87b1e4bdc5f.png)

![image](https://user-images.githubusercontent.com/50557587/155992291-486fa7d9-fd9e-4471-bbb0-166c2438c75e.png)
![image](https://user-images.githubusercontent.com/50557587/155992344-374150b8-5907-482d-b1af-333f918e4cf4.png)
![image](https://user-images.githubusercontent.com/50557587/155992482-5ba5c51a-232f-42ba-8b16-b685032d8c16.png)

Check all websites to confirm they are all working properly.  
![image](https://user-images.githubusercontent.com/50557587/156019855-b7048a70-5dd4-454e-9271-206badba76dd.png)
![image](https://user-images.githubusercontent.com/50557587/156019915-10eb2656-8d13-4f15-b49b-2b34bb8ed00b.png)


