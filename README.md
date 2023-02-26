# 4.MEAN STACK
IMPLEMENTING MEAN STACK TO UBUNTU ON AWS

# **MEAN**
Mean Stack refers to a collection of JavaScript technologies used to develop web applications. It is open-source stack offers a quick and organized method for creating rapid prototypes for web-based applications.

MEAN is comprised of four different technologies:

    MongoDB express is a schemaless NoSQL database system
    Express JS is a framework used to build web applications in Node
    AngularJS is a JavaScript framework developed by Google
    Node.js is a server-side JavaScript execution environment

![Screenshot from 2023-02-26 17-52-45](https://user-images.githubusercontent.com/77943759/221424596-b14de819-e4e6-4cef-a4fe-7e208df1a97f.png)

# **CREATE AN EC2 INSTANCE**

On aws, create an ec2 instance and save the keypair as a .pem file

![ec2instance](https://user-images.githubusercontent.com/77943759/221347451-5a9fdc7e-0180-4c87-bfbb-1d7baacdc0b4.png)

Open Terminal on your machine and change directory to the Downloads folder with `cd Downloads` where the .pem downloaded file is

Connect to the instance. Run

`ssh -i <private_keyfile.pem> username@ip-address`

# **INSTALL NODEJS**

Nodejs is a javascript runtime which we will be using in this project  to set up the Express routes and AngularJS controllers.

Run ubuntu update

`sudo apt update`

Run upgrade

`sudo apt upgrade`

![update upgrade](https://user-images.githubusercontent.com/77943759/221362496-ca4551a0-4561-4fb3-9ce2-f0eaec1b715f.png)


Add certificates

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

Install nodejs

`sudo apt install -y nodejs`

![installnodejs](https://user-images.githubusercontent.com/77943759/221362454-04df6a70-e106-464b-9301-6389342c3698.png)


# **INSTALL MONGODB**

for this app, we need a databse. we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

Run 

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

Next, run 

`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

![echo](https://user-images.githubusercontent.com/77943759/221424852-69b8a23c-807c-412c-a1b9-cdc11b4759fa.png)


Install MongoDB

`sudo apt install -y mongodb`

Start the server

`sudo service mongodb start`


To confirm the server is running, run:

`sudo systemctl status mongodb`

![mongodbstatus](https://user-images.githubusercontent.com/77943759/221412465-1cadfcc1-d902-49f4-8963-bb9705f35e3b.png)


Install node package manager - npm

![sudonpm-y](https://user-images.githubusercontent.com/77943759/221412577-b1ad4d6d-11de-4a52-a845-8b912c207310.png)

We need a body-parser package which will help us process JSON files passed in request to server. Run

`sudo npm install body-parser`

![bodyparser](https://user-images.githubusercontent.com/77943759/221412606-b40e4060-421e-4a3d-8e55-047dd7611d9a.png)


We will create a folder "Books" in the home directory and cd into it

`mkdir Books && cd Books`

Initialize npm

`npm init`

![npminit](https://user-images.githubusercontent.com/77943759/221413310-e1e0f910-0259-4d83-939c-d3f06d74ad4f.png)


Open file server.js with `vi server.js` and paste the following code inside

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
![serverjs](https://user-images.githubusercontent.com/77943759/221413268-a72dcbe9-71de-4f49-8aba-fa6f348a1b9a.png)

# **INSTALL EXPRESS AND SET UP SERVER ROUTES**

We will use Mongoose to establish a schema for the database to store data of our book register.

`sudo npm install express mongoose`

cd into Books directory and create a folder named apps, cd into the app

`mkdir apps && cd apps`

Create file routes.js

`vi routes.js`

Copy and paste the code below in routes.js

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

![editroutes js](https://user-images.githubusercontent.com/77943759/221422223-48f9bbb9-2506-427b-8320-b07ef5fefa53.png)

Create a folder called 'models' in 'apps' folder and cd into models

`mkdir models && cd models`

Create file 'book.js' and open the file

`vi book.js`

Copy and paste the code below in book.js

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
# **ACCESS THE ROUTES WITH ANGULARJS**

AngularJS provides a web framework for creating dynamic views in web applications. We use AngularJS to connect our web page with Express and perform actions on our book register. 

Change directory back into 'Books' directory

`cd ../..`

Create folder 'public' and cd into it

`mkdir public && cd public`

Create file 'script.js' 

`vi script.js`

Copy and paste the following code into script.js

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

Create file 'index.html' 

`vi index.html`

Paste the code below inside index.html

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
Change directory back into 'Books' folder

`cd ../..`

Start the server

`node server.js`

![nodeserverjs](https://user-images.githubusercontent.com/77943759/221423359-6ff2e658-5f07-4de8-ad39-ee7ab1e416cf.png)

Server is noe running and can be connected to through port 3300. We need to open this port in our ec2 instance so we can access it on the internet

![port3300](https://user-images.githubusercontent.com/77943759/221423468-0fb05c59-ee25-48d4-a7c0-9c5f64fc210f.png)

To return the `curl` of our code locally, run

`curl -s http://localhost:3300`

![curl3300](https://user-images.githubusercontent.com/77943759/221423672-4b14b43a-fb8c-4cf6-a03e-9140b6891c48.png)

This returns an html page

Open your web browser to access the server over the internet

`http://<public ip or DNS name>:3300`

To get our public ip or public DNS name run:

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4

curl -s http://169.254.169.254/latest/meta-data/public-hostname
```


![internetpage](https://user-images.githubusercontent.com/77943759/221423858-4afea0dd-2022-4e58-bfbb-2356eddaf874.png)


Done. The book web browser app is accessible over the internet.





