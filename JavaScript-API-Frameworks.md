## Why use a framework?
There are various web frameworks we can use to structure our server-side code. Express is a very popular one that has actually been the starting point for a host of [other frameworks](https://expressjs.com/en/resources/frameworks.html) too!
### Pros
A lot of the things we had to do manually when making an API purely with the node http module can be abstracted away by a framework which is designed to do specific things. This might make your development process quicker and more consistent.
### Cons
You will be dependent on an external package which itself might have a myriad of other dependencies. When choosing any package, consider if it is actively maintained. Also consider the actual size of your app. If you add dependencies that you don't really need, you might end up with a bigger bundle size than required to do the job. Note how many packages pride themselves on being 'lightweight'! It's a trade off of various different considerations.

***

## Node Packages
In order to keep all our dependencies in with our app (for ease of sharing, deploying elsewhere etc), we can make our own package. If you have `node` installed then you almost certainly have `npm` installed too. `npm` stands for node package manager and we use it to create and manage our own packages as well as install and manage external packages.

- Let's make an folder for our new app and cd into it: \
`mkdir myApp && cd myApp`
- Now we are in our project folder we can use npm to initialise our own new package. \
`npm init` - this will take you through some setup questions. If you are happy to take the default answers to each, add the `-y` flag (`npm init -y`)
- That will have created us a `package.json` file. For now we will leave it as is.

***

## Express
Let's make a new API using [Express](https://expressjs.com/)
### Installation
- We will install Express using npm: \
`npm install express --save` - the `--save` flag says 'please add this to the list of dependencies that my app requires in order to run.'
- Now we will see that a `node_modules` folder has also been created. When we share our app, we only want to share the list of dependencies, not the actual dependencies! So let's tell `git` to ignore that folder.
    - `touch .gitignore` - create a .gitignore file
    - `node_modules` - add this in your new .gitignore

### Creating a server
Let's create a new file called `server.js` \
The first thing we will need is to require Express
```js
// in server.js
const express = require('express');
```

Next we create our server... are you ready?
```js
const server = express();
```
Yep, that was it. Commonly this is actually stored in a variable called `app` but let's keep it as `server` just for consistency.

We do still need to define the location our server will be available. We'll accept the default host of 'localhost' but let's define our port:
```js
const port = 3000;
```
The port can be any unused port but 3000 is the 'classic' Express port.

Finally let's tell the server to listen out for requests:
```js
server.listen(port, () => console.log(`Express departing now from http://localhost:${port}`!))
```

### Starting your server
Okay, let's get it running! 
We could start our server by running the file by running `node server.js` in the terminal.

Since we have a node package setup anyway, let's make use of the npm scripts. Have a look in your package.json for the "scripts" key. There may well be one or two already in there. Whenever we call `npm run <script-name>` from the terminal, it will actually run whatever code we put in the value of the corresponding script name key of that "scripts" object. If it wouldn't work being called straight from the terminal, it's not going to work here either! You can have multiple scripts. You can name a script anything you like but there are some standard ones which npm will be able to run with just `npm <script-name>`. One of those is the "start" script.
```js
"scripts": {
    "start": "node server.js",
    "show-my-stuff": "ls"
},
```
Try this extremely silly "showMyStuff" script with `npm run show-my-stuff"`. If you want to be a bit extra, how about adding `"start-dev": "open ../client/index.html & node server.js"`
Now our script is set up let's run it with `npm start`.

When you get feedback that your server is running, visit your location (in this example it's `http://localhost:3000`) in a browser and you should see something! Admittedly it's not much and is in fact an indication that we've got more work to do, but Express has handled it elegantly regardless. We didn't tell it what to do when any request was made and yet it served a basic html document. If you don't believe me, go to your terminal and run `curl http://localhost:3000`.

*NB: If you get an error saying the port is already in use, use `lsof -i tcp:<port` to find the process ID (PID) that is running on that port. If you want to stop it, use `kill <PID>`.*

### CORS config
To handle our CORS (cross-origin resource sharing), we can use a piece of ***middleware*** imaginatively named '[cors](https://expressjs.com/en/resources/middleware/cors.html)'.
`npm install cors --save`
```js
// in server.js
const cors = require('cors');
// (after server has been declared)
server.use(cors());
```
This will enable access for all routes from any origin, check [the docs](https://expressjs.com/en/resources/middleware/cors.html) for all the config options!

***

### Adding routes
#### GET
Let's make our first route. It will respond to a `GET` request made to `/cats` by sending some data we have on cats:
```js
const cats = [
    { id: 1, name: 'Zelda', age: 3 },
    { id: 2, name: 'Tigerlily', age: 10 },
    { id: 3, name: 'Rumble', age: 12 },
];

server.get('/cats', (req, res) => res.send(cats);
```
Restart your server and visit or curl `http://localhost:3000` - you should get some cats!
Here's a snippet to quickly paste if you want to test it with `fetch` in the browser console or your own client-side code base.
```js
fetch('http://localhost:3000/cats').then(r => r.json()).then(console.log)
```

#### POST
Making a `POST` request is as easy as:
```js
server.post('/cats', (req, res) => res.send({message: 'POST /cats was called'}))
```
```js
// You can test it with this snippet in browser console
fetch('http://localhost:3000/cats', {method: 'POST'}).then(r => r.json()).then(console.log)
```
When we make a `POST` request we usually do send a body as well though. Do you remember the faff it was to extract the body from a request using the `http` module by itself? I certainly do! \
Let's bring in another piece of middleware to handle the parsing of a request body: [bodyParser](http://expressjs.com/en/resources/middleware/body-parser.html)
`npm install body-parser`
```js
// in server.js
const bodyParser = require('body-parser');
// after server has been declared
server.use(bodyParser.json());
```
I've used the .json bodyParser as json is the most common format to send data around in. There are plenty of alternative options though and I recommend a look through the [docs](https://github.com/expressjs/body-parser).

That's it! Now you will have access to `req.body` in your routes.
```js
server.post('/cats', (req, res) => {
    const newCat = req.body;
    const newCatId = cats.length + 1
    cats.push({ id: newCatId, ...newCat});
    res.send({message: `${newCat.name} successfully added to our collection.`})
})
```
Note that we have added in some logic to create an id for each new cat. It's better that we handle this on the server side as we have access to all the cat data, rather than expect our client to provide an id. We've added the id we created to the `req.body` from the client which should contain the cat name and age.
```js
// You can test it with this snippet in browser console
const newCat = JSON.stringify({ name: "Flora", age: 5 })
fetch('http://localhost:3000/cats', {method: 'POST', body: newCat, headers: {'Content-Type': 'application/json'}}).then(r => r.json()).then(console.log)
```

Check `http://localhost:3000/cats` and see that "Flora" has been added!

#### Other HTTP Verbs
You can find the syntax for responding to other HTTP verbs using Express, [here in the docs](http://expressjs.com/en/5x/api.html#routing-methods).

***

### MVC: A Better Structure
The **M**odel-**V**iew-**C**ontroller pattern is a popular way to structure your code. We will talk about this more further into the course but essentially it's about separation of concerns where each part of the code has a specific role. For a small app you could keep all of the code in the same file, but as it grows this becomes difficult to manage. We are going to apply this to our code early on.

#### Data

The first order of business is to move our data into its own space. It's as easy as creating a file called `data.js` in the root of our project and moving the cats data there. Remember to export this so we can have access to the data from the other parts of our app.

```js
// data.js

const cats = [
    { id: 1, name: 'Zelda', age: 3 },
    { id: 2, name: 'Tigerlily', age: 10 },
    { id: 3, name: 'Rumble', age: 12 },
];

module.exports = cats;
```

#### Model

This is where we can define what our app is all about, it handles the data and the logic. For this we are going to call upon our knowledge of Object-oriented JavaScript to set out a prototype for our cat. 

Create a folder called `models` and within it a file named `cat.js`. The first thing to do is import our cat data, then we can let JavaScript know what a Cat is.

```js
// models/cat.js

const catsData = require('../data');

class Cat {
    constructor(data) {
        this.id = data.id;
        this.name = data.name;
        this.age = data.age;
    }
}

module.exports = Cat;

```

Now we can begin to migrate some of the logic from `server.js` to here and add them as methods on our Cat. Note that we check that the data conforms with our Cat prototype at each step.

```js
// models/cat.js

class Cat {
    constructor(data) {
        this.id = data.id;
        this.name = data.name;
        this.age = data.age;
    }

    static get all() {
        const cats = catsData.map((cat) => new Cat(cat));
        return cats;
    }

    static create(cat) {
        const newCatId = catsData.length + 1;
        const newCat = new Cat({ id: newCatId, ...cat });
        catsData.push(newCat);
        return newCat;
    }
}
```

To complete the migration we now need to update our server.

```js
// server.js

...

const Cat = require('../models/cat');

...

server.get('/cats', (req, res) => {
    const catsData = Cat.all
    res.send(catsData);
}

server.post('/cats', (req, res) => {
    const data = req.body;
    const newCat = Cat.create(data);
    res.send({message: `${newCat.name} successfully added to our collection.`});
});

```

#### Controller

This is where a user can interact with our app so any requests to the server can start here and the controller will pass it to the appropriate model or view.

Let's start by creating a folder called `controllers` and a file called `cats.js`. We are then going to make use of the [express router](https://expressjs.com/en/guide/routing.html) middleware to help us create a module where we can define our routes to then import and use it in our main server. Remember to import our Cat model too.

```js
// controllers/cats.js

const express = require('express');
const router = express.Router();

const Cat = require('../models/cat');

router.get('/', (req, res) => {
    const catsData = Cat.all
    res.send(catsData);
});

router.post('/', (req, res) => {
    const data = req.body;
    const newCat = Cat.create(data);
    res.send({message: `${newCat.name} successfully added to our collection.`});
});

module.exports = router;

```

With the router setup we can condense our server file so that it only contains the basic logic we need to get up and running, with the bulk of the logic and the requests handled in the appropriate places.

```js
// server.js

const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');

const server = express();
server.use(bodyParser.json());
server.use(cors());

const port = 3000;

const catRoutes = require('./controllers/cats');
server.use('/cats', catRoutes);

server.listen(port, () => console.log(`Express departing now from http://localhost:${port}`!))

```

#### Views

This is where we could build code that presents our data, which at the moment we are going to leave to a client but can be as basic as an HTML file.

***

### Particularly useful Express docs
- [Routing Syntax for all HTTP verbs](http://expressjs.com/en/5x/api.html#routing-methods)
- [Query Parameters](http://expressjs.com/en/5x/api.html#req.query) (handling `?name=Beth&location=London`)
- [Route Parameters](http://expressjs.com/en/5x/api.html#req.params) (handling `/user/:username`)

***

## Two Relevant Topics

### CRUD
[CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) stand for Create, Read, Update, Delete. \
When working with a resource of some kind (this demo has used `cats` as a resource), we are often aiming to be able to work with it in all of these ways. In the context of cats:
**C**reate: add a new cat
**R**ead: get info about a cat
**U**pdate: update the info about a specific cat
**D**elete: delete that cat from our collection

More on this when we learn about databases but keep it in mind, when a user hits your API, is it to do one of these things?

### REST
[REST](https://en.wikipedia.org/wiki/Representational_state_transfer) stands for Representational State Transfer (protocol). This is merely a generally accepted convention that makes reading (and creating) different paths easier to understand. \
If I want to get all the cats, I expect to visit `www.animals.com/cats`, not `www.animals.com/all-the-cats` \
If I am constructing a post request to add a cat, I expect to be posting to `www.animals.com/cats`, `www.animals.com/add-this-cat`

There are 7 'official' [RESTful routes](https://gist.github.com/alexpchin/09939db6f81d654af06b) and we will cover them in more detail in due course.
