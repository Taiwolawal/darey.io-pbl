# SIMPLE TO-DO APPLICATION ON MERN WEB STACK
Task of the project to implement a web solution based MERN stack in AWS cloud.
* Signed into my AWS account and launched an EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)
* Connected to my EC2 instance through MobaXterm SSH client.

Backend configuration
- update ubuntu by running command `sudo apt update`
- upgrade ubuntu by running command `sudo apt upgrade`
- Get location of Node.js from Ubuntu repositories `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
- Install Node.js with command `sudo apt-get install -y nodejs`
- Verify node installation with command `node -v`  and `npm -v`

![node installation](https://user-images.githubusercontent.com/50557587/138787111-88c5b8c8-4219-4069-9196-eff9713fbb27.PNG)

Application Code Setup
- Create a new directory for your To-Do project` mkdir Todo`
- Confirm the Todo directory with ls command and change current directory to the new created on `cd Todo`
- Run the command `npm init` to initialise project, thus creating file name package.json

![npm](https://user-images.githubusercontent.com/50557587/138788182-1fa1250d-cf5e-4e9f-9325-76ab8ed267a7.PNG)

ExpressJs Installation
- Install with command `npm install expres`s and create a file with command  `index.js`.
- Install dotenv using command `npm install dotenv`.
- Open the index.js file with command `vim index.js` and paste code showing in the screenshot below and save in vim

![expressjs](https://user-images.githubusercontent.com/50557587/138789226-f21d63d4-6cac-407c-bc5d-17e53bad387b.PNG)

- Start server to see if it works opening the terminal `node index.js`. 
- To confirm its working the terminal should show **Server running on port 5000**

![server running](https://user-images.githubusercontent.com/50557587/138790265-abcc31d5-73c6-465c-939e-ac323ab86dc2.PNG)

- Open the EC Security group port and create a new inbound rule to open TCP port 80 and do the same for port 5000

![5000](https://user-images.githubusercontent.com/50557587/138790553-1641f9b5-9f74-4bc8-a9cd-92c4499af4bb.PNG)

- Open browser and try accessing the server's public IP http://18.189.29.133:5000/

![5000 confirm](https://user-images.githubusercontent.com/50557587/138791415-ecef0487-1870-4b7c-a0b0-680af339e6d9.PNG)

Routes Creation
- Create folder in the To-do app with command `mkdir routes`.
- Change directory to routes folder with command `cd routes` and create a file api.js with command `touch api.js`.
- Open the file with vim api.js and copy the code showing in the screenshot below.

![routes](https://user-images.githubusercontent.com/50557587/138792312-e8a2fbe7-2cb5-4e1b-9ee8-c45af144da86.PNG)




