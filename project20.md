# MIGRATION TO THE СLOUD WITH CONTAINERIZATION. PART 1 – DOCKER

In this project, we will learn practice the technology that revolutionized application distribution and deployment back in 2013! 

We are talking of Containers and imply Docker. Even though there are other application containerization technologies, Docker is the standard and the default choice for shipping your app in a container!

* Install Docker and prepare for migration to the Cloud

First, we need to install Docker Engine, which is a client-server application that contains:

  * A server with a long-running daemon process dockerd.
  * APIs that specify interfaces that programs can use to talk to and instruct the Docker daemon.
  * A command-line interface (CLI) client docker.
  
  Lets install Docker on an Ubuntu instance
  
  * Set up the repository  
  
  ```
  sudo apt-get update
  sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
  ```

* Add Docker’s official GPG key: `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`.

* Set up stabel repository
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
* Install Docker Engine
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

* Install specific version of Docker 
```
apt-cache madison docker-ce
```
![image](https://user-images.githubusercontent.com/50557587/158987233-98a9afc4-061d-4082-9806-8ec772bad752.png)

* Install a specific version using the version string from the second column, for example, 5:18.09.1~3-0~ubuntu-xenial.

 `sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io`
 
* Verify that Docker Engine is installed correctly by running the hello-world image.

![Image](https://user-images.githubusercontent.com/50557587/158987607-4f190b71-7e76-48ee-ad5d-52abd6038751.png)

In this sproject we will be using the Tooling web application we used in our previous projects, which is a PHP-based web solution backed by a MySQL database – all technologies you are already familiar with and which you shall be comfortable using by now.

So, let us migrate the Tooling Web Application from a VM-based solution into a containerized one.

MySQL in container

## Step 1: Pull MySQL Docker Image from Docker Hub Registry

Start by pulling the appropriate Docker image for MySQL. You can download a specific version or opt for the latest release, as seen in the following command: `docker pull mysql/mysql-server:latest`.  

 ![Image](https://user-images.githubusercontent.com/50557587/158990035-0eceb83d-0327-4369-add1-3d9260d59a64.png)

List the images to check that you have downloaded them successfully: `docker images ls`

## Step 2: Deploy the MySQL Container to your Docker Engine

Once you have the image, move on to deploying a new MySQL container with: `docker run --name <container_name> -e MYSQL_ROOT_PASSWORD=<my-secret-pw> -d mysql/mysql-server:latest`

* Replace <container_name> with the name of your choice. If you do not provide a name, Docker will generate a random one
* The -d option instructs Docker to run the container as a service in the background
* Replace <my-secret-pw> with your chosen password
* In the command above, we used the latest version tag. This tag may differ according to the image you downloaded
 
Then, check to see if the MySQL container is running: Assuming the container name specified is mysql-server `docker ps -a`.
 
![Image](https://user-images.githubusercontent.com/50557587/158991318-06bd2178-a7ac-4dca-91fa-1ce0f87c473b.png)

## Step 3: Connecting to the MySQL Docker Container
 
We can either connect directly to the container running the MySQL server or use a second container as a MySQL client. Let us use a second container as a MySQL client.
 
First, create a network: `docker network create --subnet=172.18.0.0/24 tooling_app_network`.
 
Creating a custom network is not necessary because even if we do not create a network, Docker will use the default network for all the containers you run. By default, the network we created above is of DRIVER Bridge. So, also, it is the default network. You can verify this by running the docker network ls command.  

![Image](https://user-images.githubusercontent.com/50557587/158992887-67025716-c8ac-444e-a932-4e1e214b8d03.png)

But there are use cases where this is necessary. For example, if there is a requirement to control the cidr range of the containers running the entire application stack. This will be an ideal situation to create a network and specify the --subnet

For clarity’s sake, we will create a network with a subnet dedicated for our project and use it for both MySQL and the application so that they can connect.

Run the MySQL Server container using the created network.

First, let us create an environment variable to store the root password: `export MYSQL_PW=password`.

Then, pull the image and run the container, all in one command like below:
 
`docker run --network tooling_app_network -h mysqlserverhost --name=mysql-server -e MYSQL_ROOT_PASSWORD=$MYSQL_PW  -d mysql/mysql-server:latest `
 
Flags used
* -d runs the container in detached mode
* --network connects a container to a network
* -h specifies a hostname
 
![Image](https://user-images.githubusercontent.com/50557587/158993969-8fcee392-4a8b-472d-b27d-fd9cd927521a.png)

As you already know, it is best practice not to connect to the MySQL server remotely using the root user. Therefore, we will create an SQL script that will create a user we can use to connect remotely.

Create a file and name it create_user.sql and add the code in the file: `CREATE USER 'user'@'%' IDENTIFIED BY 'password'; GRANT ALL PRIVILEGES ON * . * TO ''@'%';`

Run the script `docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < ./create_user.sql`.
                                                                                          
If you see a warning like this, it is acceptable to ignore: `mysql: [Warning] Using a password on the command line interface can be insecure.`
                                                                                          
## Step 4: Connecting to the MySQL server from a second container running the MySQL client utility
                                                                                                            
The good thing about this approach is that you do not have to install any client tool on your laptop, and you do not need to connect directly to the container running the MySQL server
                            
Run the MySQL Client Container: `docker run --network tooling_app_network --name mysql-client -it --rm mysql mysql -h mysqlserverhost -u  -p`.                                                                          
 
Flags used:
* --name gives the container a name
* -it runs in interactive mode and Allocate a pseudo-TTY
* --rm automatically removes the container when it exits
* --network connects a container to a network
* -h a MySQL flag specifying the MySQL server Container hostname
* -u user created from the SQL script
* -p password specified for the user created from the SQL script      
                                         
![Image](https://user-images.githubusercontent.com/50557587/158996222-8465c8a7-17e2-42c8-9bc2-2f197c398d1e.png)
 
## Step 4: Prepare database schema 
 
Now we need to prepare a database schema so that the Tooling application can connect to it.
 
i. Clone the Tooling-app repository `git clone https://github.com/darey-devops/tooling.git`.
                                                                                          
ii. On your terminal, export the location of the SQL file  `export tooling_db_schema=/home/ubuntu/tooling/html/tooling_db_schema.sql`.  
                                                                                          
iii. Use the SQL script to create the database and prepare the schema. With the docker exec command, you can execute a command in a running container. `docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < $tooling_db_schema `.
 
iv. Edit the .env file in the tooling/html folder with your db credentials used in your create_user.sql.  
 
![image](https://user-images.githubusercontent.com/50557587/158998675-73aed6df-d21b-4773-93dc-05bec8f2c406.png)
 
v. Run the Tooling App                                                                                           

Containerization of an application starts with creation of a file with a special name - 'Dockerfile' (without any extensions). This can be considered as a 'recipe' or 'instruction' that tells Docker how to pack your application into a container. In this project, we will build your container from a pre-created Dockerfile. 
 
Ensure you are inside the folder that has the Dockerfile and build your container: `docker build -t tooling:0.0.1 .` .

In the above command, we specify a parameter -t, so that the image can be tagged tooling:0.0.1 . Also, you have to notice the . at the end. This is important as that tells Docker to locate the Dockerfile in the current directory you are running the command. Otherwise, you would need to specify the absolute path to the Dockerfile.
                                                                                          
vi. Run the container: `docker run --network tooling_app_network -p 8085:80 -it tooling:0.0.1`.                                                                                         
![20complte](https://user-images.githubusercontent.com/50557587/159000380-f12d662e-115c-4959-8f01-750c99ab85ea.PNG)























