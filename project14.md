# Experience Continuous Integration With Jenkins | Ansible | Artifactory | Sonarqube | PHP.
This project is continuation of project 13.

Add two more role to the ansible 'SonarQube' and 'Artifactory'.
Install Blue Ocean plugin in Jenkins Manage Jenkins and open it when installed.        
![a1](https://user-images.githubusercontent.com/50557587/149621332-39c7a88f-039c-4af9-8cc4-6947aab1f723.PNG)      

Select Github.      
![a2](https://user-images.githubusercontent.com/50557587/149621334-5180da01-732f-40c3-b706-3dbf4c744f2d.PNG)

Connect Jenkins with Github.  
![a3](https://user-images.githubusercontent.com/50557587/149621336-8e7b7cc0-79eb-4e05-a123-d326d2f790f3.PNG)  

Login to Github and generate an access token.  
![a4](https://user-images.githubusercontent.com/50557587/149621339-78f0aed9-f376-42e9-807e-dcb3aa4b2d7d.PNG)

Copy Access Token.  
![a5](https://user-images.githubusercontent.com/50557587/149621344-e6603ce8-adf5-4d4b-83ac-d7f7e97aae0c.PNG)

Paste the token and connect.   
![a6](https://user-images.githubusercontent.com/50557587/149621346-75ab3303-256b-4469-9202-756ec1466cf9.PNG)

Create a new pipeline.   
![a7](https://user-images.githubusercontent.com/50557587/149621349-227a7a2f-f6f9-402e-a9c8-305a5bfaa040.PNG)

Click on new pipeline and click adminstration to exit Blue Ocean.
![1 1](https://user-images.githubusercontent.com/50557587/149621449-59b28d59-5982-493d-bc27-7beddb95b613.PNG)
![1 2](https://user-images.githubusercontent.com/50557587/149621499-b22f4158-be52-4306-89bc-1801ae1ccc7f.PNG)

Create a new directory deploy and start a new file Jenkinsfile inside the directory. 
![1 3](https://user-images.githubusercontent.com/50557587/149621537-0816c28f-dc7c-4d8b-8795-eaa0413ca018.PNG)

Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage.

```
pipeline {
    agent any

    stages {
      stage('Build') {
        steps {
          script {
            sh 'echo "Building Stage"'
          }
        }
      }
    }
}
```

Add push code to the main repository since there are new changes in the code. Now go back into the Ansible pipeline in Jenkins, and select configure.  
![image](https://user-images.githubusercontent.com/50557587/149622808-56af6b72-ad74-4f89-8908-ec9e102c2398.png)

In the configure section , go to the build configuration under Script path and specify the path were the jenkinsfile is, then save the settings.   
![1 5](https://user-images.githubusercontent.com/50557587/149621540-2b7aa3cc-e157-4b9d-8471-5cdf3d29aa70.PNG)

Immediately the jenkins file path is set the job will start building automatically, then open Blue Ocean in a different tab to see the build.  
![1 6](https://user-images.githubusercontent.com/50557587/149621542-05610105-4e41-443b-a029-f58de1339343.PNG)

This pipeline is a multibranch one, which means that if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.

Lets perform the action below: 
* Create a new git branch and name it feature/jenkinspipeline-stages `git checkout -b feature/jenkinspipeline-stages`.
* Paste the code snippet below an push the new changes.  
```
pipeline {
    agent any

    stages {
      stage('Build') {
        steps {
          script {
            sh 'echo "Building Stage"'
          }
        }
      }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
   }
}
````
To ensure that Jenkins see our new branch, we go to the ansible-config jobs and click on Scan Repository Now and refresh the page to show the new branch we have.  
![1 7](https://user-images.githubusercontent.com/50557587/149621543-b4de65d3-1944-4c50-8500-17993220b7ae.PNG)

Open Blue Ocean and click on the new branch we added to see the content
![1 8](https://user-images.githubusercontent.com/50557587/149621544-503a1746-cfb0-475c-ae7b-6d345eb49847.PNG)
![1 9](https://user-images.githubusercontent.com/50557587/149621571-5f09aa7f-face-4793-bdb6-ddcf88b06553.PNG)

Push the code to the main branch from feature/jenkinspipeline-stages and the following changes will take place in the main branch, just has shown in the screenshot below.    
![2 0](https://user-images.githubusercontent.com/50557587/149623513-0185592a-3dbe-4431-8ef8-67f51d0cf275.PNG)
![2 1](https://user-images.githubusercontent.com/50557587/149623517-5507ffef-0130-41c8-95a3-75ec43100ea2.PNG)

For every job created in Jenkins, it creates a workspace for each job, thus if Jenkins perform a lot of job lots of workspace will be  created which will affect storage. To avoid this type of issue, its a good practice to ensure that at the beginning of the Jenkinsfile you clean the workspace and at the end also.

Copy the content below in the Jenkinsfile and push the code. Jenkins scan each branch for any changes and it starts to build as such since we have done a push on our feature/jenkinspipeline-stages, Jenkins will start to build. Go to the Blue Ocean to see the builds.    
![2 2](https://user-images.githubusercontent.com/50557587/149623525-fe512c03-6161-47ee-b580-c3e078cb99ff.PNG)
![2 3](https://user-images.githubusercontent.com/50557587/149623526-e7e244da-e097-4a06-82dc-3ff9f80a83b7.PNG)
![2 4](https://user-images.githubusercontent.com/50557587/149623527-5a324c67-8ff1-4895-acdf-e3ef235f864d.PNG)
![2 5](https://user-images.githubusercontent.com/50557587/149623528-e617eca2-c86f-45a0-8236-ce4a91a97582.PNG)

When we merge the code with the main branch, the workspace for the main and feature branch will be deleted as specified in the Jenkinfile.  
![2 6](https://user-images.githubusercontent.com/50557587/149623538-5f85d47e-6ae6-4aaf-9da1-6704721dc8ad.PNG)
![2 7](https://user-images.githubusercontent.com/50557587/149623541-886b94e6-3599-4fbc-88fe-dd86236f786f.PNG)

## Running Ansible Playbook fronm Jenkins
Install ansible in Jenkins `sudo apt install ansible` and ansible plugin in Jenkins UI.
We need to install some dependencies for ansible to work properly, since we have some ansible modules in the roles. This will allow ansible playbook to run properly.

- yum install python3 python3-pip wget unzip git -y
- python3 -m pip install --upgrade setuptools
- python3 -m pip install --upgrade pip
- python3 -m pip install PyMySQL
- python3 -m pip install mysql-connector-python
- python3 -m pip install psycopg2==2.7.5 --ignore-installed
- ansible-galaxy collection install community.mysql
- ansible-galaxy collection install community.postgresql

After installing ansible in the Jenkins UI, go to global tool configuration under Ansible. Give descriptive name and path to ansible executabel folder by getting it via command `which ansible` and copy path ignoring the ansible part.   
![2 8](https://user-images.githubusercontent.com/50557587/149623548-86d6b091-9c8a-4376-8e3b-952442501d56.PNG)
![image](https://user-images.githubusercontent.com/50557587/149625270-180e0dc6-708e-4180-bba3-91ddd5247eb3.png)


In the deploy folder, create a file name ansible.cfg file and copy the content below inside the file
```
[defaults]
timeout = 160
callback_whitelist = profile_tasks
log_path=~/ansible.log
host_key_checking = False
gathering = smart
ansible_python_interpreter=/usr/bin/python3
allow_world_readable_tmpfiles=true

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=30m -o ControlPath=/tmp/ansible-ssh-%h-%p-%r -o ServerAliveInterval=60 -o ServerAliveCountMax=60 -o ForwardAgent=yes
````
By default when we install ansible, we have the default configuration file in /etc/ansible/ansible.cfg, now we are creating our own config file to define the default thing of our choice, how we want the ansible to run, variables to use etc.

Jenkins works with file inside the workspace folder.

The role path is not specified in the ansible.cfg file we created, it will be defined in the Jenkinsfile.

Jenkins needs to export the ANSIBLE_CONFIG environment variable, which must be declared globally and specifies where ansible.cfg file is.

```
environment {
  ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
 }
```
To run Ansible playbook via the Jenkinsfile, go to Dashoard -> Manage Jenkins -> Manage Credentials -> Credentials -> Add Credentials. Fill in the blanks, by entering content of the pem key and username (ubuntu or ec-user)
To ensure our ansible run against inventory/dev

![2 9](https://user-images.githubusercontent.com/50557587/149623561-950ce16b-71fb-4b2a-9e05-b8f8b683d97f.PNG)
![3 0](https://user-images.githubusercontent.com/50557587/149627790-36cfa564-ab98-4422-a92f-c58bab65816d.PNG)
![3 1](https://user-images.githubusercontent.com/50557587/149627792-922cf308-666a-41fb-9e00-081cce073890.PNG)

Go to Dashboard -> ansib-config -> Pipeline Sytnax, configure  path to the playbook and inventory path, ssh-user and colorized output. Generate pipeline script, copy the script and paste in the Jenkinsfile.  
![3 3](https://user-images.githubusercontent.com/50557587/149627795-fec0fff6-62af-4a88-81ed-2f5c40d3bacd.PNG)
![3 4](https://user-images.githubusercontent.com/50557587/149627796-676cab5a-ca7f-404e-b0b8-141015609f51.PNG)

Copy the content below into the Jenkinsfile.
```
pipeline {
  agent any

  environment {
      ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
    }
    
  stages{
      stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

      stage('Checkout SCM') {
         steps{
            git branch: 'feature/jenkinspipeline-stages', url: 'https://github.com/Taiwolawal/ansible-config.git'
         }
       }

      stage('Prepare Ansible For Execution') {
        steps {
          sh 'echo ${WORKSPACE}' 
          sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'  
        }
     }

      stage('Run Ansible playbook') {
        steps {
           ansiblePlaybook become: true, colorized: true, credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory/${inventory}', playbook: 'playbooks/site.yml'
         }
      }

      stage('Clean Workspace after build'){
        steps{
          cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
        }
      }
   }

}
```
Update the inventory/dev with 2 instances (nginx and db).

Push the code with the new updates in the Jenkinsfile.   

![3 5](https://user-images.githubusercontent.com/50557587/149627798-9b2459de-e783-4418-a693-0b5ac4ceff20.PNG)
![3 6](https://user-images.githubusercontent.com/50557587/149627800-2cb227e0-aad2-4c9d-a131-c9fc086dfb58.PNG)
![4 2](https://user-images.githubusercontent.com/50557587/149627822-74945072-e2d4-4779-a7e4-84d7e0abddd2.PNG)
![4 3](https://user-images.githubusercontent.com/50557587/149627823-833d1977-ab1b-4e84-a166-565c0e07642d.PNG)

If we need to deploy to other environment, manually updating the Jenkinfile is not an option, thus we need to use parametization.

The parameters need to be global and edit content of 'Run Ansibel Playbook'.

```
parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
    }
    
 stage('Run Ansible playbook') {
    steps {
       ansiblePlaybook become: true, colorized: true, credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory:'${WORKSPACE}/inventory/${inventory}', playbook: 'playbooks/site.yml'
            }
       }   
    
```

Since we included parameters in our Jenkinsfile, when you check the branch where the Jenkinfile was laucnched, you will see "Build with Parameters".

Merge code with the main branch and the branch will have "Build with Parameters"

![4 4](https://user-images.githubusercontent.com/50557587/149627834-c73cbeeb-4f65-4508-83f5-73249bec267d.PNG)
![4 6](https://user-images.githubusercontent.com/50557587/149627848-2e1aa451-ecdc-4753-bac8-c7d81859e437.PNG)
![4 7](https://user-images.githubusercontent.com/50557587/149627850-97ce6108-6eee-476f-926d-e95580aa72d4.PNG)

## CI/CD PIPELINE FOR TODO APPLICATION

- Prepare Jenkins 

Our goal here is to deploy the application onto servers directly from Artifactory rather than from git.  Here we will introduce another PHP application to add to the list of software products we are managing in our infrastructure.

Fork and clone `https://github.com/darey-devops/php-todo.git` to our instance outside the ansible-config folder.

On the Jenkins server, install PHP, its dependencies and Composer tool

`sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
sudo php /tmp/composer-setup.php
sudo mv composer.phar /usr/bin/composer`

![4 8](https://user-images.githubusercontent.com/50557587/149627853-d58502aa-96fb-4044-9686-d2a2f94a11cd.PNG)
![4 9](https://user-images.githubusercontent.com/50557587/149627858-c61fae47-f498-4168-9ef2-2e999fb7ec3d.PNG)

- Install Plot Plugin and Artifactory plugin in the Jenkins UI (Manage Jenkins)

We will use plot plugin to display tests reports, and code coverage information and the Artifactory plugin will be used to easily upload code artifacts into an Artifactory server.

Create an instance for Artifactory.

Copy the ip address into inventory/ci enviroment and configure all settings in the playbook/site.yml , roles and static assignment so as to install Artifactory.

Edit the Jenkinfile with regards the git branch to main and push code

```
stage('Checkout SCM') {
         steps{
            git branch: 'main', url: 'https://github.com/Taiwolawal/ansible-config.git'
         }
       }
 ```
 
 In Jenkins, go to the main branch and  click on Build Parameters and change to ci as such always change the inventory path in Jenkins dashboard.

![5 1](https://user-images.githubusercontent.com/50557587/149631997-5cbb54df-7115-4be6-bdfc-c78d0165d48b.PNG)

Login into the Artifactory with port 8081 and enter username and password (admin, password), create new password

Create repository -> Select Package Type -> Generic, enter Repository Key as PBL, save and finish. 

![image](https://user-images.githubusercontent.com/50557587/149633230-ba95dab0-1e85-469d-84cd-e0e585edcc87.png)

Configure the server ID, URL and Credentials, run Test Connection.  
![5 2](https://user-images.githubusercontent.com/50557587/149631999-60b3d0e3-6989-48db-bc35-f059c501c45a.PNG)

The steps above are for setting up and installation of Artifactory.

- Integrate Artifactory repository with Jenkins.

Create a Jenkinsfile in the php-todo folder, and copy the content below.

```
pipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/Taiwolawal/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```

On the database server, create database and user, the content below should be updated in the mysql roles (roles -> mysql -> defaults -> main.yml)

Ensure the Ip address used in the database is the ip for Jenkins server

![5 3](https://user-images.githubusercontent.com/50557587/149632000-f7f04d7f-bdd2-44e3-8891-82b31d4e1d57.PNG)
![5 4](https://user-images.githubusercontent.com/50557587/149632002-d71eb1d3-7401-488a-a115-6cd22db05e6f.PNG)

Push the code and build (it should be done in the php-todo folder), ensure the parameter is in dev.   
![5 5](https://user-images.githubusercontent.com/50557587/149633665-7ba008ab-5aa2-46d4-b786-6e17376b87a4.PNG)

To be sure if the database were created, launch the db (mysql-server) instance and confirm. 

```
sudo mysql 
show databases;
select user, host from mysql.user;
```

Set the bind address of the database of the MYSQL server to allow connections from remote hosts. 
`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf `

Change bind-address = 0.0.0.0 and restart mysql `sudo systemctl restart mysql`.

Create a new pipeline in blue ocean and link the php-todo git link to it, it will start building since there is a jenkinsfile in it.

Ensure you install  mysql client on Jenkins `sudo apt install mysql-client`.

In the .env.sample update the database connectivity requirements with the screenshot below. The IP address used is for the database.   
![5 7](https://user-images.githubusercontent.com/50557587/149632012-5b8bc5cc-0412-4a8d-b48f-ef07902d1e66.PNG) 

Connect to the database from Jenkins mysql -h 172.31.1.102 -u homestead -p
![5 6](https://user-images.githubusercontent.com/50557587/149632011-32a4ee8a-25a1-4980-a961-62b1ad040963.PNG)

Push the code in php-todo folder

Update the Jenkinsfile to include Unit tests step.
```
 stage('Execute Unit Tests') {
  steps {
     sh './vendor/bin/phpunit'
  } 
```

- Code Quality Analysis

This is one of the areas where developers, architects and many stakeholders are mostly interested in as far as product development is concerned. For PHP the most commonly tool used for code quality analysis is phploc.

Install phploc `sudo apt-get install -y phploc`

```
stage('Code Analysis') {
      steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'
      }
    }
```

Plot the data using the plot Jenkins plugin.
  

Bundle the application code for into an artifact (archived package) upload to Artifactory, but ensure you install zip `sudo apt install zip -y`. You can only deploy to artifactory, if a unit test has been done on it. Publish the result artifact into Artifactory.

```
stage ('Package Artifact') {
      steps {
          sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
      }
    }
    
 stage ('Upload Artifact to Artifactory') {
      steps {
        script { 
          def server = Artifactory.server 'artifactory-server'                 
          def uploadSpec = """{
                    "files": [
                      {
                       "pattern": "php-todo.zip",
                       "target": "PBL/php-todo",
                       "props": "type=zip;status=ready"
                       }
                    ]
                 }""" 

          server.upload spec: uploadSpec
        }
      }
  
    }
 ```
Deploy the artifact to the dev environment by launching Ansible pipeline, we are deploying it to ansible-config/main repository. We need to ensure that todo server details are already in the inventory/dev and updated in playbook/site.yml

```
stage ('Deploy to Dev Environment') {
      steps {
        build job: 'ansible-config/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
      }
    }
 ```
 
 Push codes from php-todo repository so as to deploy, to ensure artifact is download as stated in the deployment.yml, ensure you get password generated  from the Artifactory server. 
 
To get the password, click Set Me Up and enter default password. 
![image](https://user-images.githubusercontent.com/50557587/149657158-faf9f2a5-2fbd-426e-b754-f04663593d92.png)  
![image](https://user-images.githubusercontent.com/50557587/149657183-be09196c-22af-4228-a050-2d26f6103f95.png)

 Even though we have implemented Unit Tests and Code Coverage Analysis with phpunit and phploc, we still need to implement Quality Gate to ensure that ONLY code with the required code coverage, and other quality standards make it through to the environments.

To achieve this, we need to configure SonarQube – An open-source platform developed by SonarSource for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.

## SonarQube

Create a role for sonarqube via ansible galaxy for installation of Sonarqube.

Create an instance for Sonarqube, the minimum requirement is 4GB RAM and 2 vCPUs and update the Ip details in ansible-config inventory/ci and site.yml for complete installation.

Push the code and run from the build , change parameter to CI, some bugs came with the screeshot below, so we had to run ansible via command line.  
![6 1](https://user-images.githubusercontent.com/50557587/149654661-47b6a3f2-9c7d-499b-b441-70f1a6adea39.PNG)

To run the command we need to update the roles_path in ansible.cfg located in the deploy folder, just for the purpose of the Sonarqube installation `roles_path=/home/ubuntu/ansible-config/roles`.

After updating the roles_path in the ansible.cfg, enter the following command in the terminal:
`export ANSIBLE_CONFIG=/home/ubuntu/ansible-config/deploy/ansible.cfg`

Before running ansible-playbook ensure the ansible machine can talk to the server via SSH agent, by confirming `ssh-add -l`.

Run ansible-playbook `ansible-playbook -i inventory/ci playbooks/site.yml`.  
![6 2](https://user-images.githubusercontent.com/50557587/149654664-091e5aee-a6a8-4941-bf38-e273f3524ef8.PNG)
![6 3](https://user-images.githubusercontent.com/50557587/149654666-6a20bd94-2259-4953-98b3-fc62aa6541db.PNG)

Connect to instance via port 9000, password and username is both admin

In Jenkins, install SonarScanner plugin. Navigate to configure system in Jenkins. Add SonarQube server as shown below:  
![image](https://user-images.githubusercontent.com/50557587/149658088-b63b3f96-bcde-40e0-825c-468a8286eea8.png)

Generate authentication token in SonarQube
![image](https://user-images.githubusercontent.com/50557587/149658098-5b3b35d4-b986-4a78-a394-ef554afa5437.png)

Configure Quality Gate Jenkins Webhook in SonarQube – The URL should point to your Jenkins server http://{JENKINS_HOST}/sonarqube-webhook/  
![image](https://user-images.githubusercontent.com/50557587/149658109-f92cf739-4bd9-4c2b-a6e6-c685a8b90f79.png)

Setup SonarQube scanner from Jenkins – Global Tool Configuration
![image](https://user-images.githubusercontent.com/50557587/149658127-92020e75-a2bb-4d78-a25e-7d7ab678e1a4.png)

Update Jenkins Pipeline to include SonarQube scanning and Quality Gate, it should be placed before "Package Artifact".

````
stage('SonarQube Quality Gate') {
      when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
        environment {
          scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
            }
            timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
        }
    }
 ````
 
 We have to update `sonar-scanner.properties` for the build to work:
 
 ```
 cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/conf/
 sudo vi sonar-scanner.properties
 ```
Add configuration related to php-todo project

```
sonar.host.url=http://<SonarQube-Server-IP-address>:9000
sonar.projectKey=php-todo
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
sonar.php.exclusions=**/vendor/**
sonar.php.coverage.reportPaths=build/logs/clover.xml
sonar.php.tests.reportPath=build/logs/junit.xml
```

In Sonarqube stage, it has some bugs, bad coverage and code smells as such, we do not want it to be pushed to any environment. 

We will update the Jenkinsfile with

```
 stage('SonarQube Quality Gate') {
      when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
            }
            timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
        }
    }
`````

![image](https://user-images.githubusercontent.com/50557587/149657046-8eeae283-d63b-4b2e-b53d-073d17265b62.png)

  
![5 8](https://user-images.githubusercontent.com/50557587/149632014-17f55fd3-a154-4a26-88a5-bb1044d91275.PNG)   
![5 9](https://user-images.githubusercontent.com/50557587/149632015-8f9cbe1e-dfe5-4a7c-b8c2-7e9808180bc2.PNG)  
![6 0](https://user-images.githubusercontent.com/50557587/149632016-ace8004c-4602-46b9-bf20-4d72b4339bd2.PNG)   




![6 4](https://user-images.githubusercontent.com/50557587/149654669-248eaa90-2b9e-40a0-af30-90e420eeedf5.PNG)
![6 5](https://user-images.githubusercontent.com/50557587/149654670-a7982644-687d-429a-966a-9d986c3091c0.PNG)

![image](https://user-images.githubusercontent.com/50557587/149658973-fe43ddbf-8ed1-41de-993c-ccf6d240e029.png)






![6 6](https://user-images.githubusercontent.com/50557587/149657508-d648542d-b8e8-4332-b553-66c5f8b49e7c.PNG)
































