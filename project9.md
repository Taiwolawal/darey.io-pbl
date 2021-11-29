# Continous Integration Pipeline For Tooling Website.
- The task of the project is to start automating part of our routine tasks with free and open source automation server  - JENKINS.
- In the project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in Github https://github.com/Taiwolawal/tooling will be automatically be updated to the Tooling website.
- The updated architecture will look like the screenshot below upon completion of this project.  
![p9](https://user-images.githubusercontent.com/50557587/142725468-bdc96c8a-9602-4285-ba6d-bef6090dfd63.PNG)

- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins".
- Install JDK `sudo apt update` `sudo apt install default-jdk-headless`.
- Install Jenkins  
![1](https://user-images.githubusercontent.com/50557587/142726479-a7181a2d-696f-4d88-98fb-f67d6063cc4c.PNG)

- Confirm Jenkins is up and running `sudo systemctl status jenkins`.
- Create a new inbound rule in the EC2 Security Group to TCP port 8080    
![2](https://user-images.githubusercontent.com/50557587/142726600-0ad35ae6-802a-4cb9-87a7-fa4fd1d97f28.PNG)
 
- Perform initial jenkins set up from your browsers `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`.
- You will prompted to provide a default admin password. Retrieve from your server `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.   
- On the next page it shows after entering the admin password, choose suggested plugins.    
![j1](https://user-images.githubusercontent.com/50557587/143429676-a174a6bf-ae5f-4df0-a614-2b7332bcbbb0.PNG)

- Enable webhooks in your Github repository.     
![j14](https://user-images.githubusercontent.com/50557587/143795431-be13add2-58c8-473f-b6c8-297b82d375a9.PNG)

-  Go to Jenkins web console, click “New Item” and create a “Freestyle project”.
-  To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself.
-  In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository and click save.  
![j15](https://user-images.githubusercontent.com/50557587/143795766-a3c4049b-40ab-42e4-99ab-d650003861e0.PNG)

- Run the build. For now we can only do it manually. Click “Build Now” button, if you have configured everything correctly, the build will be successfull and you will see it under the first build:   
![j16](https://user-images.githubusercontent.com/50557587/143796814-b8227c3b-9682-4292-ae73-2071c52cf892.PNG)

- We can open the build and check in “Console Output” if it has run successfully. As shown below, the build ran successfully:   
![j17](https://user-images.githubusercontent.com/50557587/143796907-b7337ded-498e-4886-ab50-b7a86892c40b.PNG)

- But this build does not produce anything and it runs only when we trigger it manually and we would make some adjustments to fix this.
- Click “Configure” your job/project and add two configurations. 
- Configure triggering the job from GitHub webhook.  
![j18](https://user-images.githubusercontent.com/50557587/143797080-f08cc846-cd20-4963-a0bf-c433eb66d027.PNG)
 
-  Configure “Post-build Actions” to archive all the files - files resulted from a build are called “artifacts”.   
![j19](https://user-images.githubusercontent.com/50557587/143797190-0f582ff8-6062-4ffa-b021-59dc7ab3573a.PNG)

- Go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch. We can see that a new build has been launched automatically (by webhook) and you can see its results - artifacts, saved on Jenkins server. 
- By default, the artifacts are stored on Jenkins server locally `/var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`.

## Configure Jenkins to copy files to NFS server via SSH
- The artifacts are saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.
- Install "Publish Over SSH" plugin" from the Manage Jenkins and select Manage Plugins under System Configuration.  
![j20](https://user-images.githubusercontent.com/50557587/143797871-63312a5e-aabd-41b4-b9b2-81a3348073d1.PNG)

- Configure the job/project to copy artifacts over to NFS server. On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.
- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server.  
![j21](https://user-images.githubusercontent.com/50557587/143798455-d84c2755-d2f1-4b0c-9b0b-db7f06096d5f.PNG)

- Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”.   
![j22](https://user-images.githubusercontent.com/50557587/143798513-cdb6e841-ee2b-4db2-8292-059beb78200d.PNG)

- Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories - so we use "**".
- If you want to apply some particular pattern to define which files to send - use this syntax.   
![j23](https://user-images.githubusercontent.com/50557587/143798579-fc3fe6bf-c2d8-41e5-834f-e0eda940d209.PNG)

- Make changes in the README.MD file in the git account and confirm if it will sync along with jenkins and show in the NFS server.    
![git2](https://user-images.githubusercontent.com/50557587/143798923-4b973e98-ec44-47e8-92ad-648368ae2354.PNG) 

- Below is the output from the NFS server, checking the README.MD file  
![git1](https://user-images.githubusercontent.com/50557587/143799067-4a62660d-c818-49c9-a1f3-df0f98ab7741.PNG)


























