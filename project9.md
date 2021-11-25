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

- Ebanle webhooks in your Github repository.
- Configure your Jenkins freestyle project choosing git repository
