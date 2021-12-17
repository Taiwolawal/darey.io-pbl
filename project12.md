# Ansible Refactoring And Static Assignments (Import and Roles).
- In this project, we will continue working with ansible-config repository and make some improvements to our code.
- Now we need to refactor our Ansible code, create assignments, and use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.

## Step 1 - Jenkins Job Enhancement
- Lets make some changes to our Jenkins job, every new change in the code creates a seperate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins servers with each subsequent change.
- We will enhance it by introducing a new Jenkins project/job, which we will require Copy Artifact plugin.
- In the Jenkin-Ansible server, create a new directory called ansible-config-artifact, we will store there all artifacts after each build.
- Change permissions to this directory, so Jenkins could save files here.  
![1](https://user-images.githubusercontent.com/50557587/146604672-cee76b09-7b00-4da2-98ab-3715df10b8a8.PNG)

- Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plgin without restarting Jenkins.  
![2](https://user-images.githubusercontent.com/50557587/146606477-bc3187de-d0f2-49c0-a279-5a402ec89165.PNG)

- Create a new Freestyle project and name it save_artifacts.
- This project will be triggered by completion of the existing ansible project. Configure it accordingly.   
![3](https://user-images.githubusercontent.com/50557587/146606591-b29f9f08-0feb-40a7-80d8-274c63b58d0c.PNG)
![4](https://user-images.githubusercontent.com/50557587/146606599-595b2f47-5f1a-4fd9-a5f8-a38dd5055b01.PNG)

- The main idea of save_artifacts project  is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.   
![q](https://user-images.githubusercontent.com/50557587/146607237-1b3bd85b-de67-49ee-b0bc-53d7ad4795f2.PNG)

- Test your set up by making some change in README.MD file inside your ansible-config repository (right inside master branch).
- If both Jenkins jobs have completed one after another – you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your main branch.     
![5](https://user-images.githubusercontent.com/50557587/146607510-01338ddc-f28b-4e42-8ce9-ee5c52aef8cf.PNG)
![6](https://user-images.githubusercontent.com/50557587/146607523-5e57a41e-7ad0-44d5-9c00-83db1855cec3.PNG)

## Step 2 - Refactor Ansible Code By Importing Other Playbooks Into site.yml
- Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and creat a new branch, name it refactor. Let see code re-use in action by importing other playbooks
- Within playbooks folder, create a new file and name it site.yml, this file will now be considered as an entry point into the entire infrastructure configuration. The site.yml will become a parent to all other playbooks that will be developed.
- Create a new folder in root of the directory and name it static-assignment. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work.
- Move common.yml file into the newly created static-assignments folder.   
![9](https://user-images.githubusercontent.com/50557587/146609611-3266b127-7410-4fee-b548-1349f4c65943.PNG)

- Inside site.yml file, import common.yml playbook.   
![9 1](https://user-images.githubusercontent.com/50557587/146609891-2720987d-1962-43e1-b300-c23b5fe3fde5.PNG)

- The folder structure should look like this:   
![9 2](https://user-images.githubusercontent.com/50557587/146609896-4d011b12-1e75-4636-aa6c-44c9c116bbc2.PNG)

- Create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.
- Update site.yml with import_playbook: ../static-assignments/common-del.yml and run against dev servers.   
![9 3](https://user-images.githubusercontent.com/50557587/146609901-1eb5c8ac-5086-4e4f-8c6f-36242836fce4.PNG)
![9 4](https://user-images.githubusercontent.com/50557587/146609904-78f6c448-90c6-4f29-9e7a-238f4a1598c8.PNG)
![9 5](https://user-images.githubusercontent.com/50557587/146610826-a79df255-e9ef-47ad-8bd8-2d39f8472171.PNG)
![9 6](https://user-images.githubusercontent.com/50557587/146610829-7ff49cef-6801-42fe-845f-2ba3fc428841.PNG)

## Step 3 - Configure UAT Webservers with a role 'Webserver'
- Launch 2 EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT.  
![9 7](https://user-images.githubusercontent.com/50557587/146610834-0ea94848-d4d8-4c4c-992d-fa562053f0ba.PNG)

- To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.
- Create a roles directory in the root, cd into the directory, use an Ansible utility called ansible-galaxy, `mkdir roles && cd roles && ansible-galaxy init webserver`.  
- The entire folder structure should like below.     
![9 8](https://user-images.githubusercontent.com/50557587/146610840-6a955680-6912-4bd1-a044-18aaeaf9fdf7.PNG)

- After removing unnecesary directories and files, the roles structure should look like this.         
![10 0](https://user-images.githubusercontent.com/50557587/146610853-76308551-3418-4dfd-af10-2e3549780fe4.PNG)  
![9 9](https://user-images.githubusercontent.com/50557587/146610849-b55f0fb3-c27c-48ae-980c-e62dfcee13b9.PNG)    

- Update the inventory ansible-config/inventory/uat.yml file with IP addresses of the 2 UAT Webservers.   
![i](https://user-images.githubusercontent.com/50557587/146616805-08f86f59-ea54-4064-a50b-b11a8d764773.PNG)

- In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.  
![10 1](https://user-images.githubusercontent.com/50557587/146616902-4273470c-29f5-4ca6-9015-54eb6d0a0b28.PNG)

- It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following.
- Install and configure Apache (httpd service)
Clone Tooling website from GitHub https://github.com/Taiwolawal/tooling.git.
Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
Make sure httpd service is started

-  The main.yml will consist of the following tasks.  
![10 2](https://user-images.githubusercontent.com/50557587/146616927-4bcee5da-4238-4bf2-b46a-4a857545eab0.PNG)

## Step 4 - Reference 'Webserver' role.
- Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.      
![t](https://user-images.githubusercontent.com/50557587/146617608-bb1383d0-35ed-4944-91b5-71366315c73b.PNG)

- Remember that the entry point to our ansible configuration is the site.yml file therefore , we need to refer uat-webservers.yml role inside site.yml. The site.yml will look like:         
![vc](https://user-images.githubusercontent.com/50557587/146617614-4dceb523-f5c3-4e72-bfeb-7da98d944b24.PNG)

## Step 5 - Commit & Test.
- Commit your changes, create a Pull Request and merge them to master (main) branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config/ directory.
- Now run the playbook against the uat inventory.   
![10 3](https://user-images.githubusercontent.com/50557587/146616930-f25acccf-4e64-4c20-8ff2-a7eb2cab3791.PNG)
![10 4](https://user-images.githubusercontent.com/50557587/146616942-9c41f5c4-f701-4c35-a735-9fbfa9ee3735.PNG)
![10 5](https://user-images.githubusercontent.com/50557587/146616946-1cb8489f-0b9e-4299-9aac-f2b9ec984f4f.PNG)

- You should be able to see both of the UAT Web servers configured and you can try to reach them from your browser: `http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`.

- The Ansible architecture now looks like this:      
![w](https://user-images.githubusercontent.com/50557587/146618308-2465389b-92e7-460e-b83e-d27ef39bb2a5.PNG)

