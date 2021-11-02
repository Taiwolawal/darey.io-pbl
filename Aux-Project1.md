# AUX PROJECT 1: SHELL SCRIPTING

- The task of the project is to onboard 20 new Linux users onto a server. 
- Create a shell script that reads a csv file that contains the first name of the users to be onboarded.
- The script will create each user on the server and add to an existing group called 'Developers'
- The script will first check for the existence of the user on the system, before attempting to create the user.
- The user that is being created also must have a default home folder.
- Each user should have .ssh folder within its home folder. if it does not exist , then it will be created.
- For each user's ssh configuration , we will create an authorized key file and add below the public key.


- Launch EC2 Instance with all configuration needed.
- Create a new file onboard.sh `vi onboard.sh` and copy the script screenshot below into the file and save.
![script](https://user-images.githubusercontent.com/50557587/139960599-72b3e38a-6cca-4e37-a87d-e3b91da7d522.PNG)

- Use the scp command to copy the pem key, onboard.sh  and the ec2 instance, all to the home directory and click on the enter key
- `scp -i taiwo-ec2.pem onboard.sh ubuntu@18.191.168.185:~/;`
- The onboard.sh has been copied to the remote server successfully.
![Aux1](https://user-images.githubusercontent.com/50557587/139961422-44efe69f-26f2-4b0c-b08c-0d15ecc82c8a.PNG)

- We can connect to the remote server to confirm that the onboard.sh file is on the remote server
- `ssh -i "taiwo-ec2.pem" ubuntu@ec2-18-191-168-185.us-east-2.compute.amazonaws.com`

- In the remote server we create a directory Shell and cd into it `mkdir Shell && cd Shell`.
- We move the onboard.sh file into the Shell folder `mv onboard.sh /home/ubuntu/Shell/`.
- We create some files `touch id_rsa id_rsa.pub names.csv`.
- In the id_rsa.pub file we paste the follow content
![public key](https://user-images.githubusercontent.com/50557587/139958657-b47af460-820a-4568-b55f-069cb80b8945.PNG)

- In the id_rsa file, paste the following content
![private](https://user-images.githubusercontent.com/50557587/139962963-6fd6a383-516f-446e-877f-5cfd5e1ce584.PNG)

- In the names.csv file, add names required.

 ![names](https://user-images.githubusercontent.com/50557587/139963366-375d2690-cc96-4c8d-a01d-8d0029a49135.PNG)

- Create developers group `sudo groupadd developers`.
- Make the onboard.sh a executable file `sudo chmod +x onboard.sh`.
- Trying to run the onboard.sh (script) without the sudo permission will not be successful.
![permmsion](https://user-images.githubusercontent.com/50557587/139964122-5d2a0f64-a6aa-48db-905e-7c207bf1fb2a.PNG)

- Switch to a super user `sudo su` i.e now a root inside a shell folder, now run the script 
![Aux4](https://user-images.githubusercontent.com/50557587/139964630-12829473-6de1-4d65-9cdf-82dfe594d2a7.PNG)

- To confirm if all the users has been created type `ls -l /home/` in the Shell folder.
![Aux5](https://user-images.githubusercontent.com/50557587/139964896-d0954cdc-55a6-42cb-81f4-7a73bdd50971.PNG)

- To confirm if developers group has been created getent group developers.
![developers](https://user-images.githubusercontent.com/50557587/139965080-c5496e22-8166-475f-8365-3ff6935f9193.PNG)

- Test a few of users randomly to confirm  if they can connect to the server using the private key and the public key.
- In the onboard.sh script, we have already inputted the public key inside the file in which the private key recognise as the key-pair.
- Open a new terminal, create a pem key and paste the private key inside vi aux-proj.pem. Below is the screenshot of the private key.
![private](https://user-images.githubusercontent.com/50557587/139962963-6fd6a383-516f-446e-877f-5cfd5e1ce584.PNG)

- Connect with any of the user's created `ssh -i aux-proj.pem Arsenal@18.191.168.185`, ensure the pem key is protected `sudo chmod 600 aux.proj.pem ` .

![fffff](https://user-images.githubusercontent.com/50557587/139966396-d80ded85-e67b-4888-9494-3f70ac4d5300.PNG)
