# Ansible Configuration Management
- In project 7-10, we performed a lot of manual operations for setting up virtual servers, installing and configuring required softwares.
- The project aim is to automate most of the tasks with Ansible Configuration Management.

##  Install And Configure Ansible on EC2 Instance
- Update the name tag on our Jenkins EC2 Instance to Jenkins-Ansible, it is the server will we use to run our playbook.
- In our Github account create a new repository named ansible-config.
- Configure Jenkins build job to save the repository content everytime a change occurs.
- Create a new freestyle project "ansible" in Jenkins and point it to the "ansible-config" repository.  
![git3](https://user-images.githubusercontent.com/50557587/145273956-56ef5679-e0e4-4288-bd1e-c62ae13a1b0b.PNG)

- Configure Webhook in Github and set webhook to trigger ansible build. 
![git7](https://user-images.githubusercontent.com/50557587/145275018-32bc3a85-c340-4343-afec-d3e12e2801c4.PNG)

- Test the setup by making some change in README>MD file in main branch and make sure the build starts automatically and Jenkins saves the file.       
![git5](https://user-images.githubusercontent.com/50557587/145275357-904fed3c-64af-424c-96a2-d489b76894d3.PNG)

- Install Ansible `sudo apt update`, `sudo apt install ansible` in the instance.
- Create a folder named ansible and cd into it `mkdir ansible && cd ansible`.
- Clone the ansible-config repository in the ansible folder `git clone https://github.com/Taiwolawal/ansible-config.git`.

## Ansible Development
- Cd into the ansible-config folder and create a branch `git checkout -b feature/prj-11-setup` and switch to it.
- In the branch, create two directories playbooks and inventory respectively `mkdir playbooks && mkdir inventory`.
- The playbooks folder will be used to store all playbook files and the inventory folder will be used to keep the hosts (all servers information) organised.
- In the playbooks folder, create the first playbook named common.yml `touch common.yml`.     
![prj-11](https://user-images.githubusercontent.com/50557587/145277707-9387e7cc-87d1-4682-9841-208ad1d06570.PNG)

- In the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) respectively `touch dev.yml staging.yml uat.yml prod.yml`.  
- Update the inventory/dev.yml to start configuring.  
![pj7](https://user-images.githubusercontent.com/50557587/145278980-9980320e-5df2-40b9-b82b-859d33aa085e.PNG)

- Update the path of the inventory, so that ansible knows where to get the inventory file `sudo vi /etc/ansible/ansible.cfg`.  
![pj4](https://user-images.githubusercontent.com/50557587/145282342-0960a3bf-df50-4aff-8a65-f5d5e937878e.PNG)

- Ansible uses TCP port 22 by default,which means it needs to ssh into target servers (nfs, db, webservers, lb).  
- Since we have to ssh to all servers, we have to first enable password authentication on all target servers including the client and set a password.    
![pj2](https://user-images.githubusercontent.com/50557587/145280409-9ffbd27a-22e2-4b3d-ab1b-c6c8faa018c4.PNG)   
![pj1](https://user-images.githubusercontent.com/50557587/145280461-f8764642-fb9c-4175-9b10-585a43e53293.PNG)  

- Next we ssh to all remote servers to confirm connectivity.   
![pj3](https://user-images.githubusercontent.com/50557587/145281084-791e16aa-1d34-4d24-b845-f7827255afb5.PNG)

- To ensure that when we run ansible it is able to connect to all the servers, we need to ensure all servers have the public key for easy access.

- Ensure that the Jenkins-Ansible server has private and public keys `ssh-keygen -t rsa` which will be used to authenticate.
- Copy the public key to all servers that will be connected to which allows access to the servers, thus it is registered on the servers as an authorized login method.  
![pj5](https://user-images.githubusercontent.com/50557587/145284622-c003a8bd-5094-42d6-a64e-3abb2cb58e8e.PNG)

## Create a Common Playbook
- It is time to start giving Ansible instructions on what needs to be performed on all servers listed in inventory/dev.yml .
- Update the playbook/common.yml file with the following code: 
![pj9](https://user-images.githubusercontent.com/50557587/145287408-1f01244e-9ddc-4b4a-9805-1197c0d4df57.PNG)

- Run the playbook and ensures it works properly. 
![pj10](https://user-images.githubusercontent.com/50557587/145287192-b1ed380a-9608-42e5-96e3-e08148b95f07.PNG)

## Update GIT with the latest code.
- All the configurations we did are all on our local machine, so we need to push all this changes to Github.  
![pj11](https://user-images.githubusercontent.com/50557587/145288634-9ec726df-e744-44b7-8b75-2686fe413c61.PNG)   
![pj12](https://user-images.githubusercontent.com/50557587/145288696-d88c967a-b7c0-4627-bd0e-2d3c0d548080.PNG)  
![p13](https://user-images.githubusercontent.com/50557587/145288960-c1711635-ec1c-40fa-8396-4cb119b6e516.PNG)   
![p14](https://user-images.githubusercontent.com/50557587/145289057-fa0a9e72-b1b7-44b4-af83-6d077274d5ac.PNG)
![p15](https://user-images.githubusercontent.com/50557587/145289064-efeb400f-b618-446f-9e8f-fd9e32c70a84.PNG) 
![pj16](https://user-images.githubusercontent.com/50557587/145289339-d6071245-8fed-40bb-b7b6-4e46a0ada22f.PNG)

- The push we did are all on on our branch, hence we need to sync the files with the main branch. 
![pj17](https://user-images.githubusercontent.com/50557587/145289519-bd3c42da-624d-470a-8bb7-99f06647ca27.PNG) 
![pj18](https://user-images.githubusercontent.com/50557587/145289532-49da6999-98ed-4d86-81d1-4ac9eb2d036f.PNG)













