# Automate Infrastructure With IAC Using Terraform Part 3.

This is the continuation of Project 17. In this project we are going to modularize our project by creating different folder e.g VPC, EFS, RDS, ALB.

We are going to create a folder named modules, and inside the folder, create folders named VPC, Security, EFS, RDS, ALB, Autoscaling, Compute. This folders created, we are going to copy files related to the name of the folder e.g For the VPC folders, the files needed to setup our VPC are internet-gateway, Nat-gateway, Routes etc

With a module, it is reuseable by different engineers , the only thing to do is just to tweak some parameters input to your desire taste.

Also create variables.tf file in all the created folders, so the content of the folders will be variables.tf file and other files related to the name of the folders.

The content of the modules folder will be like the screenshot below:  
![image](https://user-images.githubusercontent.com/50557587/154669777-289fe6f2-a2cd-46ba-9934-69dddacc3029.png)

To make use of the content in the modules, you need to use the keyword module and the folder name you are referencing, and state the source where the folder is, just like what you have in the screenshot below:  
![image](https://user-images.githubusercontent.com/50557587/154671456-4a3bc570-5840-4683-8770-6a3794072d95.png)



