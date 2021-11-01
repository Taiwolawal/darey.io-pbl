# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

The task of the project is to implement a Simple Book Register web form using MEAN stack.

The Mean Stack is a combination of the following components:
- MongoDB (Document Database): Stores and allows to retrieval of data.
- Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
- Angular (Front-end application framework) – Handles Client and Server Requests.
- Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user

Launch your EC2 Instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM) and connect.

## Installations and steps requires
- update ubuntu `sudo apt update` and upgrade  `sudo apt upgrade`. 
- Add certificates `sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates` and `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
- Install Nodejs `sudo apt install -y nodejs`.
- Install MongoDB (storing data in flexible JSON like documents)
`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`
`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`
- The book register will have book name, isbn number, author, and number of pages.
- Install MongoDB `sudo apt install -y mongodb`.
- Start the service `sudo service mongodb start`. 
- Verify that the service is up and running `sudo systemctl status mongodb`.
- Install npm (Node package Manager) `sudo apt install -y npm`.
- Install body-parser package which helps to process JSON files passed in requests to the server `sudo npm install body-parser`.
- Create a folder named 'Books' and cd into the directory `mkdir Books && cd Books`.
- In the Books directory , initialize npm `npm init` and a file named server.js.
- Copy and paste the code below into the server.js file
![project4-2](https://user-images.githubusercontent.com/50557587/139684939-20aaebf8-d2c5-44ed-b7f6-60cdb3929f08.PNG)

- Install Express `sudo npm install express`, a minimal and flexible Node.js web application framework which provides feature for web and mobile application.
 It will be used to pass book information to and from our MongoDB database.
- Install Mongoose `package sudo npm install mongoose`. We will use Mongoose to establish a schema for the database to store data of our book register.
- In the Books folder create a folder name 'apps' and cd into it `mkdir apps && cd apps`.
- Create a file name 'route.js' and paste the code below.
![project4-3](https://user-images.githubusercontent.com/50557587/139686351-3ad36f5f-cd76-4156-be46-db1d59a49c63.PNG)
 
- Access the routes with AngularJS which provides a web framework for creating dynamic views in your web applications.
- AngularJS will be used to connect to our web pag with Express and perform action actions on our book register.
- Change directory back to 'Books' folder `cd ../..`
- Create a folder named 'public' and cd into it `mkdir public && cd public`.
- Add a file named 'script.js' and paste the code below into it.
![project4-5](https://user-images.githubusercontent.com/50557587/139687207-2b9244ab-04c4-4a92-b108-f76980fe1bb1.PNG)

- In the public folder, create a file named 'index.html' and paste the code below into it.
![into](https://user-images.githubusercontent.com/50557587/139687668-638e614f-4410-4a4f-a518-b672cb13acbf.PNG)

- Change the directory back up to 'Books' `cd ..`
- Start the server by running `node server.js`.
- To confirm its working fine, we will see the screenshot below.
![project4-6](https://user-images.githubusercontent.com/50557587/139688026-023a74f5-a0cf-4ac4-be74-d2217ac2dfe1.PNG)

- To connect and see the content on a the internet, we need  to open TCP port 3300 on AWS Web Console for our EC2 Instance.
![project4-7](https://user-images.githubusercontent.com/50557587/139688476-c4c22e96-fd72-4b1c-b684-3112dd9c04e5.PNG)

- Now we can access our Book Register web application from the internet with a browser using Public IP address or Public DNS name.
- This is the content of the Web Book Register Application using a browser with information entered. Check the screenshot below.
![project4-8](https://user-images.githubusercontent.com/50557587/139689091-d72781c0-7eb0-4d7e-92f3-f66ef2e3e190.PNG)

- This is the content of our server showing the information entered in the screenshot above.
![project4-9](https://user-images.githubusercontent.com/50557587/139690514-12717f69-1fdc-4737-9c55-cc93556111db.PNG)
