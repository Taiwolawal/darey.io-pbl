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

## Step 2 -Refactor Ansible Code By Importing Other Playbooks Into site.yml
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

## Configure UAT Webservers with a role 'Webserver'







![9 7](https://user-images.githubusercontent.com/50557587/146610834-0ea94848-d4d8-4c4c-992d-fa562053f0ba.PNG)



![9 8](https://user-images.githubusercontent.com/50557587/146610840-6a955680-6912-4bd1-a044-18aaeaf9fdf7.PNG)


![9 9](https://user-images.githubusercontent.com/50557587/146610849-b55f0fb3-c27c-48ae-980c-e62dfcee13b9.PNG)

![10 0](https://user-images.githubusercontent.com/50557587/146610853-76308551-3418-4dfd-af10-2e3549780fe4.PNG)










- 

