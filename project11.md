# Ansible Configuration Management
- In project 7-10, we performed a lot of manual operations for setting up virtual servers, installing and configuring required softwares.
- The project aim is to automate most of the tasks with Ansible Configuration Management.


- 

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
- Cd into the ansible-config folder and create a branch `git checkout -b feature/prj-11-setup` and switch to it.
- In the branch, create two directories playbooks and inventory respectively `mkdir playbooks && mkdir inventory`.
- The playbooks folder will be used to store all playbook files and the inventory folder will be used to keep the hosts (all servers information) organised.
- In the playbooks folder, create the first playbook named common.yml `touch common.yml`.     
![prj-11](https://user-images.githubusercontent.com/50557587/145277707-9387e7cc-87d1-4682-9841-208ad1d06570.PNG)


