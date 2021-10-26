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

Models
- The app is going to make use of Mongodb which is a NOSQL database, thus we create a model.
- The model will be used to define database schema.
- To create a Schema ana a model, we will mongoose which is a Hode.js package.
- Return back to the Todo folder and install mongoose `npm install mongoose`.
- Create a new folder `mkdir models` and change directory to newly created models folder `cd models`.
- Create a file inside the model folder `touch todo.js`.
- Open the todo.js file  `vim todo.js` and paste the code showing in the screenshot below.

![mongoosee](https://user-images.githubusercontent.com/50557587/138932066-b1978bcf-b685-45fc-9241-93d29e995baf.PNG)

- Return back to the routes directory and open api.js `vim api.js`, delete the code inside `:%d`.
- Paste the code showing in the screenshot below in the api.js file.

![router new](https://user-images.githubusercontent.com/50557587/138932707-44b7b791-cf00-49e4-ac44-04e9b257ef71.PNG)

MongoDB Database
- A database is needed to store all our data.
- MongoDB database provided by mLab will be used for storing data.
- Sign up for shared cluster free account ideal for our usecase.
- Change time of deleting entry from 6 hour to 1 week.

![ip address](https://user-images.githubusercontent.com/50557587/138952815-2f9e9c72-67c4-4e92-9e5c-c1d2fa2244d3.PNG)

- Create a MongoDB database and collection inside mLab
- Create a file in Todo directory `touch .env`.
- Open the file `vi.env` and paste the following

`DB = mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority`

- username, password, dbname and network address should be inputted accordingly.

![connect to cluster](https://user-images.githubusercontent.com/50557587/138953755-0b2d4091-0bb6-45ce-b7e4-87a9d262790f.PNG)

![connecting](https://user-images.githubusercontent.com/50557587/138954945-b60d70e3-6ddb-4be7-849d-df9c539762ac.PNG)

- We update the `index.js` to reflect the use of `.env`, so that Node.js can connect to the database.
- Simply delete the existing content in `index.js`.
- Open the file `vim index.js`, delete content `:%d`.
- Paste in the code showing in the screenshot below.

![code entered](https://user-images.githubusercontent.com/50557587/138955765-bad371a9-e631-407f-bf47-5de7ea9eef79.PNG)

- Start the server using the command `node index.js`.
- A message displaying "Database connected successfully" will appear.

![connected successful](https://user-images.githubusercontent.com/50557587/138956042-d5c12395-0fdf-4270-a268-fea6bc1b1fc7.PNG)
































