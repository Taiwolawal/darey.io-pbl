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
- Install with command `npm install express` and create a file with command  `index.js`.
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

Test Backend code without Frontend using Restful API
- Postman will be used to test our API.
- Open Postman to create POST request to the API using http://18.216.223.201:5000/api/todos
- Set Key to Content-Type and Value to application/json

![postman](https://user-images.githubusercontent.com/50557587/138957734-6ddac0df-53c8-4f90-8fcc-23050cac8090.PNG)

- This will return status: 200 Ok, if it was successfully posted.

![postman_result](https://user-images.githubusercontent.com/50557587/138958016-9b2c7bfd-caaf-4ff4-8571-10667f8fb91b.PNG)

- Create a GET request to your API using http://18.216.223.201:5000/api/todos, which displays all existing records from the database.

![get_request](https://user-images.githubusercontent.com/50557587/138958395-59fdd51c-c413-4572-bc7f-56e9e08d5d5a.PNG)

Frontend Creation

- Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API.
- We will use the create-react-app command  `npx create-react-app client` to scaffold the app.
- In the Todo directory, run  `npx create-react-app client`.
- Create a folder in the Todo directory called client where all react code will be added.
- Before testing the react app, some dependencies needs to be installed.
- Install concurrently `npm install concurrently --save-dev`. It used to run more than one command simultaneously from the same terminal window.
- Install nodemon `npm install nodemon --save-dev`, it is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
- In Todo folder, open the package.json file, the section containing "scripts":{..} should be replaced with the code 

`"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},`

![json](https://user-images.githubusercontent.com/50557587/138969772-17b46d62-bdd0-485c-9cd3-daeefa79e9ac.PNG)

- Configure proxy on package.json
- Change directory to 'client' and open package.json `vi package.json`.
- Add the key value pair "proxy": `"http://localhost:5000"` to the package.json file.
- The purpose of adding the proxy configuration is to make it possible to access the application directly from the browser using http://localhost:5000 rather than the including the entire path like http://localhost:5000/api/todos.
- Revert to the Todo directory and run `npm run dev `.
- To be able to access the application from the internet, open TCP port 3000 on EC2 by adding a new security group.
- To confirm if address is working, run http://18.216.223.201:3000/

![react](https://user-images.githubusercontent.com/50557587/138971301-6e5e80b1-aacf-4e79-8e34-b537369f45d9.PNG)


Creating React Components
- Advantage of react is that it makes use of components, which are reusable and also makes code modular.
- There will be two stateful components and one stateless component.
- From the Todo directory run `cd client` and move to ` cd src`, create another folder called components `mkdir components`.
- Move into components folder `cd components`.
- Inside the components directory create three  `touch files Input.js  ListTodo.js Todo.js`.
- Open Imput.js file and paste the code below

`import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input`

- To make use of Axios, which is a Promise based HTTP client for the browser and node.js.
- Move to src folder, to clients folder and install Axios `npm install axios`.
- Go to component directory `cd src/components`.
- Open ListTodo.js `vi ListTodo.js` and paste the following code.

`import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
`
- Then in your Todo.js file you write the following code

`import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
`
- Move to the src folder and open App.js and paste the code below.

`import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;`

- In the src directory open the App.css `vi App.css` and paste the following code.

`.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
`

- In the src directory open the index.css `vim index.css` and copy and paste the code below:

`body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}`

- Go to the Todo directory and run `npm run dev`.
- Assuming no error , it should work properly

![project completion](https://user-images.githubusercontent.com/50557587/138975729-680db92a-ce1f-4912-8ff4-689d2913060c.PNG)






























