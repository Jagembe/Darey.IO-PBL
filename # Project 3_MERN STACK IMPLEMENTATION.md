# Project 3 MERN STACK IMPLEMENTATION

**Step 1** BACKEND CONFIGURATION

Updated  and upgraded ubuntu using the following cmdlet:

`sudo apt update`

`sudo apt upgrade`

Getting location of Node.js software from Ubuntu repositories using the following cmdlet:

`curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`

Output:

![screenshot](https://user-images.githubusercontent.com/58337007/149603434-17230ca5-78e3-4a5b-8fee-dc85a3e19e81.png)

Installed Node.js  and npm (a package manager for Node like apt for Ubuntu) with the command below:

`sudo apt-get install -y nodejs`

Output:

![screenshot](https://user-images.githubusercontent.com/58337007/149604661-79e97df7-ce7c-48df-a85e-fa26962a9232.png)

Verified the node installation with the commands below:

`node -v`

`npm -v`

Output of above commands:

![screenshot](https://user-images.githubusercontent.com/58337007/149605976-0cfccc51-64cd-45a2-9c4c-ae28717fbe4b.png)

***Application Code Setup***

Created a new directory for my To-Do project using the following commands:

`mkdir Todo`

Verified that the 'Todo' directory is created with cmdlet:

`ls`

Changed current directory to the newly created one using:

`cd Todo`

Output of the above commands:

![screenshot](https://user-images.githubusercontent.com/58337007/149606201-9eab14f5-10f8-4a18-8003-6bb985cb3f51.png)

Used the following command  to initialise my project so that a new file named package.json will be created:

`npm init`

Output of the above command:

![screenshot](https://user-images.githubusercontent.com/58337007/149607064-2a24fb98-7b45-432d-a42b-01e783725f34.png)

Confirmed that the package.json file is created using the following command:

`ls`

Output of the above command:

![screenshot](https://user-images.githubusercontent.com/58337007/149607154-458d84f4-c389-4b7f-a218-1b6312603052.png)

**Step 2 ** INSTALLING EXPRESSJS:

Installed express using npm:

`npm install express`

Output of the above command:

![screenshot](https://user-images.githubusercontent.com/58337007/149607402-8dd3a266-d789-40d2-bfbb-fce43c0eec3f.png)

Created a file 'index.js' with the cmdlet below:

`touch index.js`

Ran `ls` to confirm that the 'index.js' file is successfully created:

`ls`

Output of above cmdlet:

![screenshot](https://user-images.githubusercontent.com/58337007/149608409-c7be96e5-be05-4265-abe1-8261cff9fc87.png)

Installed the 'dotenv' module using the cmdlet:

`npm install dotenv`

![screenshot](https://user-images.githubusercontent.com/58337007/149608516-90e06d33-e1a6-4649-acba-5a86b7fb41ea.png)

Opened the index.js file with the cmdlet below:

`vim index.js`

Typed the code below into it and saved.

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
Output of typing the above code:

![screenshot](https://user-images.githubusercontent.com/58337007/149608763-08e13bf5-991f-4ca8-9896-a6dade68da9f.png)

Started the server to see if it works on the port 5000, within the same directory as the index.js file using cmdlet:

`node index.js`

Output of the above cmdlet:

![screenshot](https://user-images.githubusercontent.com/58337007/149608951-920b66d3-6631-4067-893b-6721e9013d73.png)

Created an inbound rule to open TCP port 5000 in AWS EC2 Security Groups as follows.

![screenshot](https://user-images.githubusercontent.com/58337007/149609412-3d3879b0-a5c2-4110-bed6-2d3b00ac686c.png)

Opened browser and accessed my servers Public IP / Public DNS name followed by port 5000 with the following output:

![screenshot](https://user-images.githubusercontent.com/58337007/149609675-e0e2ed44-5d2e-49e8-b4e7-f32231ed44d2.png)

Created a folder 'routes' as follows:

`mkdir routes`

Changed directory to 'routes' folder.

`cd routes`

Created a file api.js with the cmdlet below:

`touch api.js'

Opened the file with the cmdlet below:

`vim api.js`

Inserted the code below in the file:

```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```

Output of the above cmdlets:

![screenshot](https://user-images.githubusercontent.com/58337007/149610600-54f0ac41-b116-4e9a-aa14-82e4e1d67485.png)


***MODELS***

Changed directory back to 'Todo' folder and installed Mongoose (a Node.js package that makes working with mongodb easier):

`npm install mongoose`

Output of the above cmdlet:

![screenshot](https://user-images.githubusercontent.com/58337007/149610761-7b724d98-a379-4db6-99e0-017e754fc46f.png)

Created a new folder 'models' using the cmdlet below:

`mkdir models`


***MONGODB DATABASE***

Acquired a free tier MongoDB Database offered as a database as a service solution (DBaaS) from mLab.

![screenshot](https://user-images.githubusercontent.com/58337007/149641151-90df0b06-2c3f-48f6-92fd-9a68755396de.png)

Created a file in the Todo directory and named it '.env'. using the cmdlet below:

`touch .env`
`vi .env`

Added the connection string to access the database in it as shown below:

![screenshot](https://user-images.githubusercontent.com/58337007/149641275-b362dd70-f2de-4c61-8e7a-fa3bf4278434.png)

Updated the index.js to reflect the use of .env so that Node.js can connect to the database as follows:

- opened the file with vim index.js
- deleted existing content in the file, and updated it with 'esc', typed :%d, and hit enter
- pressed i to enter the insert mode in vim and entered the code below:

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

Output of above commands and processes:

![screenshot](https://user-images.githubusercontent.com/58337007/149641865-516176c4-5a99-4e23-b261-6577a0a1424e.png)

![screenshot](https://user-images.githubusercontent.com/58337007/149641918-e26eeadf-63dd-47f0-b55a-be0163fef744.png)

Started server using the command:

`node index.js`

Output:

![screenshot](https://user-images.githubusercontent.com/58337007/149644772-0f16570d-5847-434c-96d5-0d8688af7229.png)

***Testing Backend Code without Frontend using RESTful API***

Created a  few POST requests to the API in Postman using the url http://ec2-54-152-30-38.compute-1.amazonaws.com:5000/api/todos.
 
Output of POST requests sending new tasks to the To Do list:

![screenshot](https://user-images.githubusercontent.com/58337007/149707934-bdb6583c-c197-4d71-a7aa-e852b03c2f32.png)

Created a GET request to the API on http://ec2-54-152-30-38.compute-1.amazonaws.com:5000/api/todos

Output of GET request to the API retrieving existing list of tasks:

![screenshot](https://user-images.githubusercontent.com/58337007/149708725-c2154469-469f-4689-a1c8-f83042ff9ce3.png)

Created a DELETE request to the API on  http://ec2-54-152-30-38.compute-1.amazonaws.com:5000/api/todos/61e4ed1c033778545f02599d

Output of DELETE request to delete a task from the To Do list.

![screenshot](https://user-images.githubusercontent.com/58337007/149709384-e21373d2-c104-441b-b07d-141c0c9baa6c.png)

Backend is successfully created.


**Step 2** FRONTEND CREATION

Ran the following cmdlet in the Todo directory to create a directory called 'client':

`npx create-react-app client`

Output of installing react.

![screenshot](https://user-images.githubusercontent.com/58337007/149719464-a8551de0-3dff-4bd4-920b-f9c95133bd92.png)

Installed the following dependencies (concurrently and nodemon) before testing the react app using the following cmdlets:

`npm install concurrently --save-dev` 

`npm install nodemon --save-dev`

Updated the package.json file using the code below:

```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```
Output of the above steps:

![screenshot](https://user-images.githubusercontent.com/58337007/149721372-8e8d8ad4-48b5-415c-aded-61822e5d16bd.png)

![screenshot](https://user-images.githubusercontent.com/58337007/149721602-fef5cd38-d17f-4f41-b2b2-5e7ac6ead2a3.png)

![screenshot](https://user-images.githubusercontent.com/58337007/149722236-5a336d72-ce66-4a6b-a6b4-f2e0413bf165.png)

Changed directory to 'client' and updated the package.json file using the following cmdlet:

`vi package.json`

Added the key value pair in the package.json file "proxy": "http://localhost:5000"

Output of the above.

![screenshot](https://user-images.githubusercontent.com/58337007/149724037-c220efe1-9fdf-42ab-afa7-146a220bfc76.png)

Ran the app using the following cmdlet from the Todo directory:

`npm run dev`

Output of above cmd.

![screenshot](https://user-images.githubusercontent.com/58337007/149733397-f8cee825-7573-48a7-85a4-5d89240f45df.png)

Created React components as follows using the following cmdlets:

`mkdir components && cd components && touch Input.js ListTodo.js Todo.js`

Updated the Input.js file with the following code:

```
import React, { Component } from 'react';
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

export default Input
```
cd into client directory and ran the following cmdlet:

`npm install axios`









