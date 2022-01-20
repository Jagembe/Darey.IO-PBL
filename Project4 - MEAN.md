# PRJECT 4: MEAN STACK IMPLEMENTATION

### MEAN Stack is a combination of following components:
  1. MongoDB (Document database) - Stores and allows to retrieve data.
  2. Express (Back-end application framework) - Makes requests to Database for Reads and Writes.
  3. Angular (Front-end application framework) - Handles Client and Server Requests.
  4. Node.js (JavaScript runtime environment) - Accepts requests and displays results to end user.

#### Step 0: - Preparaing prerequisites:
Created a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) image.

Output of instance creation:
![screenshot](https://user-images.githubusercontent.com/58337007/150178323-40f34241-90f6-4a20-b8aa-7619643a2758.png)

#### Step 1: Install NodeJs

Updated ubuntu server using the following cmdlet:

`sudo apt update`

Upgraded ubuntu server using the following cmdlet:

`sudo apt upgrade`

Added certificates using the following cmdlets:

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

Installed NodeJS using the following cmdlet:

`sudo apt install -y nodejs`

Output of the above cmds:

![screenshot](https://user-images.githubusercontent.com/58337007/150181298-5b880eeb-68b0-4cb9-bfdd-ebfd293bc0e2.png)

#### Step 2: Install MongoDB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
mages/WebConsole.gif

Installed Mongodb key using the following cmdlet:

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

Installed MongoDB using the following cmdlet:

`sudo apt install -y mongodb`

Output of installing mongodb:

![screenshot](https://user-images.githubusercontent.com/58337007/150186439-a2aa71d2-ac7b-46db-8ee0-585deb1ad676.png)

Started the server using the following cmdlet:

`sudo service mongodb start`

Verified that the service is up and running using the following cmdlet:

`sudo systemctl status mongodb`

Output of the cmdlet above:

![screenshot](https://user-images.githubusercontent.com/58337007/150187329-9f2dbfa0-72f5-4883-b30c-90ccb962e8ca.png)

Installed npm - Node package manager using the following cmdlet:

`sudo apt install -y npm`

Output of instaling npm - Node package manager.

![screenshot](https://user-images.githubusercontent.com/58337007/150200979-6d0e0a90-bdb7-47e0-b301-bf2bec3be1d1.png)

Installed body-parser package to help in processing JSON files passed in requests to the server using the cmdlet below:

`sudo npm install body-parser`

Ouput of the above cmdlet:

![screenshot](https://user-images.githubusercontent.com/58337007/150201725-21f39300-436d-4ca6-97ee-f4e8b4e7b82d.png)

Created a folder names 'Books' and cd into 'Books' using the following cmdlet:

`mkdir Books && cd Books`

Initialized npm project in the Books directory using the cmdlet below:

`npm init`

Output of the above cmdlet:

![screenshot](https://user-images.githubusercontent.com/58337007/150202903-a3de592a-fc8a-45cb-af33-7305b34b76a0.png)

Added a file named server.js to the Book directory using the cmdlet below:

`vi server.js`

Updated the server.js file with the following code:

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

Output of the above process:

![image](https://user-images.githubusercontent.com/58337007/150203996-b9fbe6c8-094c-4222-bc7a-660a5efa15e4.png)


#### Step 3: Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. I will use Express in to pass book information to and from the MongoDB database.

I will also use Mongoose package which provides a straight-forward, schema-based solution to model my application data. I will use Mongoose to establish a schema for the database to store data of the book register.

The following cmdlet installs Express:

`sudo npm install express mongoose`

Output of the above cmdlet:

![screenshot](https://user-images.githubusercontent.com/58337007/150206885-950e9eb9-a874-4e78-ac7c-6cfcd4b48798.png)

Created a folder named 'apps' in the Books directory and created a file named routes.js using the following cmdlet:

`mkdir apps && cd apps`

Created a file named 'routes.js'  and updated with the following code, in the apps folder:

```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

Created a folder named 'models' in the 'apps' folder and cd into it using the following cmdlet:

`mkdir models && cd models`

Created a file named 'book.js' and updated it with the following code:

```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```

#### Step 4 - Accessing the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this project, I use AngularJS to connect the web page with Express and perform actions on the book register.

Changed directory back to 'Books' using the following cmdlet:

`cd ../..`

Created a folder names 'public' using the cmdlet below:

`mkdir public && cd public`

Added a file named 'script.js' and updated it with the following code:

```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```

Created a file named 'index.html' in the 'public' folder using the cmdlet below:

`vi index.html`

Udated the 'index.html' file with the code below:

```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

Changed the directory back to 'Books' using the cmdlet:

`cd ..`

Started the server by running the cmdlet:

`node server.js`

Output of the server running:

![screenshot](https://user-images.githubusercontent.com/58337007/150242475-21dd04f7-1d8b-4379-a5b2-1346330d1925.png)

Tested what curl command returns locally using the following cmdlet:

`curl -s http://localhost:3300`

Otput from the curl command above:

![screenshot](https://user-images.githubusercontent.com/58337007/150242835-cf10d370-b6d9-4967-98d8-7f608ad551f6.png)

Udated my Security Group associated with the ubuntu server to allow traffic via port 3300 as follows:

![screenshot](https://user-images.githubusercontent.com/58337007/150247643-4b6f4678-d325-4ab6-9ec0-efc903c94f52.png)

Accessed the Book Register web application from the internet with a browser using Public IP address / Public DNS name:

![screenshot](https://user-images.githubusercontent.com/58337007/150247994-0555ea49-90c7-49d8-9652-f9a5cb2e8403.png)

![screenshot](https://user-images.githubusercontent.com/58337007/150258447-3d3892a1-a322-4011-8279-4af92cae7319.png)

The Web Book Register Application project is complete.
