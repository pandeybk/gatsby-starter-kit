---
title: Hello World Node JS
categories: ['helloworld']
---

# What is Node.js?

Node.js is a javascript environment built for creating traditional programs. There is no HTML, there is no DOM, there is no browser at all. The JavaScript code executes directly from a .js file. Node.js essentially lets you use javascript just like you use ruby, python or java. It provides a set of built in libraries for performing common tasks such as file management or networking.
It's extremely popular right now because JavaScript is one of the most popular programming language specially for web apps, and anyone who knows JavaScript in the browser can learn to use JavaScript in Node.js quite easily. Node.js includes a built in libraries for creating web servers. You can create a single page website in less than a dozen lines of code.
It also scales exceptionally well, with less effort, compared to PHP or RoR. A single Node.js process can handle tens of thousands of simultaneous requests before experiencing any performance lag. If you're familiar with Java, it's very similar to Java Virtual Machine (JVM). JVM provides a runtime environment for Java applications while Node.js provides runtime environment for JavaScript environment.

 
# How to install Node.js?

Installation procedure for Node.js depends on your operating system

## Installation on windows

- Open the official page for  [Node.js downloads](https://nodejs.org/en/download//)  and download Node.js for Windows by clicking the **Windows Installer** option
- Run the downloaded Node.js  `.msi`  Installer - including accepting the license, selecting the destination, and authenticating for the install. ( This requires Administrator privileges, and you may need to authenticate)
- To ensure Node.js has been installed, open `Command Prompt` or `Powershell` and run  `node -v`  in your console - you should get something like  `v8.12.0`
- Update your version of npm with  `npm install npm --global`
- Congratulations - you've now got Node.js installed.

## Installation on macOS
-  Open the official page for [Node.js Downloads page](https://nodejs.org/en/download/) and download Node.js for macOS by clicking the "Macintosh Installer" option
- Run the downloaded Node.js  `.pkg`  Installer
-  Run the installer, including accepting the license, selecting the destination, and authenticating for the install.
-  You're finished! To ensure Node.js has been installed, run  `node -v`  in your terminal - you should get something like  `v8.12.0`

## Installation on Linux
- Installation on Linux is highly depended on distribution you're using, I'll highly recommend using your package manager to install Node.js
- Open the official page for [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/) and follow guide for your current distribution.

# What is NPM?
You must be wondering what's `npm`? Well `npm` stands for Node Package Manager, it's a utility which can be used to install libraries and other software in Node.js ecosystem from the command line interface. So, if you want to install any libraries you'd run `npm install <package-name>` from the terminal  and it will magically fetch the files from npm registry in `node_modules/` directory from where command was run.

# What is MongoDB?

MongoDB is an open-source, document database designed with both scalability and developer agility in mind. MongoDB are kind of like key-value stores, Instead of storing data in rows and columns as one would with a relational database such as MySQL, MSSQL, etc, MongoDB stores JSON documents in collections with dynamic schemas. MongoDB's document makes it easy for you to store and combine data of any structure


# MongoDB installation

MongoDB instalation is also depended on operating system, you can follow the official steps for MongoDB installation

-  [Install MongoDB Community Edition on Windows](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)
- [Install MongoDB Community Edition on macOS](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
- [Install MongoDB Community Edition on Linux](https://docs.mongodb.com/manual/administration/install-on-linux/)
If you're not familiar with MongoDB, it's recommended that you read the [Official Tutorial](https://docs.mongodb.com/manual/tutorial/getting-started/)

# Building a CRUD application with Node.js and MongoDB
You'll build simple REST API using Node.js for a book library. Finished project will be able to Create, Remove, Update and Delete books in a library but before you can start building this CRUD application, you'd need to install bunch of dependencies from npm.

**express.js** - Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web apps.
**mongoose.js** - Mongoose provides a straight-forward, schema-based solution to model your application data. It includes built-in type casting, validation, query building, business logic hooks and more, out of the box.
**body-parser** - Package that can be used to handle JSON requests.

Create a directory for your project (call it `LibraryRestApp)` and navigate it to it using your terminal or command-prompt and install the above dependencies as 
```
npm install --save express body-parser mongoose
```

## Creating the server

Create a new file  `main.js` inside the `LibraryRestApp` directory and open the newly created file named  `main.js`  and write down the following code.

```js
const express = require('express');  
const bodyParser = require('body-parser');
const app = express();
const PORT = 8081;  
  
app.listen(PORT, () => {  
    console.log(`Server is Up and Running at ${PORT}`);  
});

```

The above lines of codes, imports `express`, `body-parser` using the `require()` , creates instance of `express` and listens for connection on a port specified in `PORT` constant. To finally run this little server app, on your terminal write this
```bash
node main.js
```
If everything goes right, you should see the message by `console.log` function

![success server](https://i.imgur.com/YZn3UIz.png)

## Creating model for books

Now you have a server that is ready to handle requests, Next step would be to create model schema for instances of *Book* that you will be storing . Create a new file called as  **book_model.js**  inside the `LibraryRestApp` directory with the following content

```js
const mongoose = require('mongoose');  
const Schema = mongoose.Schema;

let BookSchema = new Schema({  
    name: {type: String, required: true},  
    author: {type: String, required: true},
    isbn : {type: Number, required: true},
});
  
module.exports = mongoose.model('Book', BookSchema);
```

Here first mongoose is imported using `require()` and schema for `Book` is defined which in our app just consists of *name*, *author* and *isbn* number. This is quite similar to schema in relational database but you'd be storing book data using above schema in the MongoDB. Last line just exports the schema model so it can be used by other files in our project.

## Setting up body-parser

This application would be working with JSON data so you also need for configure it to use body-parser. It's an npm package that is used to parse the incoming request bodies in a middleware.

In your  **main.js**  file, add the following couple of lines.

```js
app.use(bodyParser.json());  
app.use(bodyParser.urlencoded({extended: false}));
```

## Connecting app to the Database

If you've successfully installed and started the MongoDB, you can easily connect application to the database.

All you have to do is open `main.js` file and add the following code in it. 

```js
let Book = require('./book_model');
const  mongoose  =  require('mongoose');
let  db_url  =  'mongodb://localhost:27017/LibraryApp';
mongoose.connect(db_url, { useNewUrlParser:  true });

mongoose.Promise  =  global.Promise;
let  db  =  mongoose.connection;
db.on('error', () =>  console.log('MongoDB connection error'));
```

`db_url` consists of your database URL followed by database name (after `/`), This is a local database and there it should be set to localhost. Last line just informs of connection failure to the database so your `main.js` should look like

```js
const  Book = require('./book_model');
const  express  =  require('express');
const  bodyParser  =  require('body-parser');
const  app  =  express();

const  mongoose  =  require('mongoose');
const  PORT  =  8081;
let  db_url  =  'mongodb://localhost:27017/LibraryApp';

mongoose.connect(db_url, { useNewUrlParser:  true });
mongoose.Promise  =  global.Promise;
let  db  =  mongoose.connection;
    
app.listen(PORT, () => {
console.log(`Sever is Up and Running at ${PORT}`);
});
db.on('error', () =>  console.log('MongoDB connection error'));
```


## Setting up routes

To keep this project nice and simple, you'll be writing routes in `main.js`. However keep in mind that this is usually not done in production applications. When writing real world application you'd split routes in various files according to their functionality. But you might be wondering what the heck is a route? Simply put, *Route* refers to how an application’s endpoints (URIs) respond to client requests. For example your applications might list out all the books stored in database on a specific URL `/list`. So when a user requests this URL ( `http://some-fancy-website/list'`), Your application will retrieve and list all the books stored to the client.

For this application you will have the following routes

`/list` - To list all the books in JSON format to the client, no parameters needed.
`/add` -   To add a new book to the application. *name* , *author* and *isbn* will be provided as `POST` JSON data .
`/remove` - To remove books from database, *ID* will be provided as `POST` JSON data.
`/update` - To update existing entry in database, *ID*, name* , *author* and *isbn* will be as `POST` JSON data.

### Create operation

First you'd need to define handler for `/add` and using JSON passed by `POST` you will need to create instance of `Book` and save it in MongoDB. Add the following code to `main.js`
```js
app.post('/add', (req, res)=> {
	let  book  =  new  Book({
			name:  req.body.name,
			author:  req.body.author,
			isbn :  req.body.isbn
			});

	book.save((err) =>{
		if (err) { res.send({'msg' :  'Failed'}) }
		res.send({'msg':  'Success'})
	});
});
```
The above code first extracts name, author and isbn from POST data, (hence `app.post()`) and then creates new instance of `Book` from those properties and finally saves it with `save()` and returns result of that operation. 
To test this functionality you can use [cURL](https://curl.haxx.se/) or [Postman](https://www.getpostman.com/). If you want to use curl,  issue following command in your terminal
```bash
curl --header "Content-Type: application/json"   --request POST   --data '{"name":"1984","author":"Orwell","isbn":"9780451524935"}'   http://localhost:8081/add
```

![curl test](https://i.imgur.com/zqf9BB6.png)
You can even verify in your MongoDB shell 
![mongo shell](https://i.imgur.com/iMdQnfn.png)

### Read Operation

For listing all the books, you'd need to add `GET` handler with `/list` route. Handler will fetch all the data from the database and return to the client, Add the following code to `main.js`

```js
app.get('/list', (req, res )=> {
	Book.find({}, (err, books)=> {
		if(err) res.send({'msg': 'Failed'})
		res.send(books);
	});
});
```
These few lines of code, will try to find every `Book` in the database and return it as `JSON` response.   ( Note `app.get()`)
To test this you can use following curl command
```bash
 curl --request GET http://localhost:8081/list
```
![read](https://i.imgur.com/zUxZI5U.png)


### Update operation

Update the the trickiest of all of the CRUD operation, like above you'd have to define `/update` handler, So add the following code to `main.js`

```js
app.post('/update', (req, res )=> {

	Book.findById(req.body.id , (err, book)=> {
	
		if (err) res.send({'msg' :  'Failed'})
		
		book.name  =  req.body.name;
		book.author  =  req.body.author;
		book.isbn  =  req.body.isbn;
		
		book.save((err, updatedBook)=> {
			if (err) res.send({'msg' :  'Failed'})
			res.send(updatedBook);
		});
});
```
You'll have to pass *id* along with *name*, *author* and *isbn* to the API in JSON and it will be updated in the database. First you'd have to search the book by id in the database and then update its properties and then finally save it. 

```bash
 curl --header "Content-Type: application/json"   --request POST   --data '{"id":"5b98e61fcfb7e69874403178","name":"1984","author":"George Orwell","isbn":"9780451524935"}'   http://localhost:8081/update
```
Here author's name was updated to "George Orwell" from "Orwell"

![updated](https://i.imgur.com/VxALCeM.png)

### Remove operation

Remove operation is simplest of all operation, you'd have to look for document by id the the database and then remove just remove it, again like above you'd have to define `/remove` handler, Add the following code to `main.js`

```js
app.post('/remove', (req, res)=>{

	Book.findOneAndRemove({_id :  req.body.id}, ()=>{
		res.send({'msg':  'Deleted'})
		});
})
```

The above code finds the book by *id* passed as `POST` and then removes it from the database, simple as that.
To test it from the curl, use the following command (btw make sure to use correct id)
```bash
curl --header "Content-Type: application/json"   --request POST   --data '{"id":"5b98e61fcfb7e69874403178"}'   http://localhost:8081/remove
```

![remove op](https://i.imgur.com/yR25Xff.png)

As you can see book with that id was deleted from the database.

# Conclusion

This article demonstrated how you can build simple REST API using Node.js and MongoDB. Full code is available at https://github.com/pandeybk/hello-world-nodejs for your reference.
