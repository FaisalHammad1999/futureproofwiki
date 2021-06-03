## Some definitions
### client-side vs server-side
**Client-side** code is that which runs on the user client. This is usually a browser or a native mobile application. \
**Server-side** code is that which runs on the server. The server is a computer that can be contacted, usually by many clients, and will serve back data as requested.

### frontend vs backend
**Frontend** refers to what a user sees and how they interact with it. Code related to this may be client-side or server-side. \
**Backend** refers to anything that runs ***server-side*** that isn't directly related to what the user experiences.

As a heavy over-generalisation, one might say that the frontend is what your user sees and the backend is how it works.

So far, aside from our test suites, we have been writing frontend code and mostly been working with JavaScript in the browser. \
Now we're going to setup our own backend server!

## JS outside of the Browser with NodeJS
Different browser vendors have different [JavaScript Engines](https://en.wikipedia.org/wiki/JavaScript_engine) that power a JS runtime as part of all the other browser offerings. \
Now that we're leaving the browser, we are going need an alternative runtime environment. [NodeJS](https://nodejs.org/) is powered by Google's [V8 engine](https://v8.dev/) and brings in other dependencies such as [Libuv](https://libuv.org/)

### Installing nodejs
Download and installation instructions can be found on the [NodeJS website](https://nodejs.org/) \
Check what version of node you have installed with `node -v`

### Creating a server with the node http module
Let's create a new file called `server.js` \
In order to send data back and forth on the internet, we will need to use HTTP. The built-in `http` node module will provide us with useful methods for this. 
```js
// in server.js
const http = require('http');
```
Now we want to make sure we can actually handle a request made to our server. Lets make a function that can deal with such requests. This function will later be passed to the `createServer` method which will pass along a request object and a response object. Usually we'd be paying more attention to these objects but for now let's just respond with a 200 (success) response and a body of 'Hello, client!' regardless of what is received.
```js
const requestListener = (request, response) => {
  response.statusCode = 200;
  response.end('Hello, client!');
};
```
Next let's define the location our server will be available. We can state a ***host*** and a ***port***. You can think of the host as an address and the port as a door. eg `host: Baker Street`, `port: 221B`. If you don't explicitly define these, they will be set to 'localhost' and 8080 anyway but if you want to change those defaults, this is how you can do that!
```js
const host = 'localhost' // let's make this server available privately on this computer. If you don't define 
const port = 8000 // this can be any unused port you like. 8000 and 8080 are just a couple of the 'standard' dev server ports
// When our server is running, we will be able to access it at http://localhost:8000 on our local machine
```

We have the essential elements now, we're just missing the actual server. Let's use the `createServer` method and pass it our `requestListener` function.
Finally we will tell the server we created to `listen`. The three arguments of port, host and cb function (run when the server has successfully started) are optional but it's good to know what you're getting.
```js
const server = http.createServer(requestListener);
server.listen(port, host, () => console.log(`All cylinders now firing on http://${host}:${port}!`))
```
Okay, let's get up and running! In your terminal, run `node server.js` and wait! When you get feedback that your server is running, visit your location (in this example it's `http://localhost:8000`) in a browser (or `curl` it in a new terminal) and you should see the message from your server!

### Retrieving data from your backend
Now that we've creating a server, got it running, and it can handle requests (albeit in a very basic manner), let's try and access it from our own client-side code base. We should be quite familiar with `fetch` by now so let's use that. You can test this from the browser console or in your own codebase.
```js
// client-side js
fetch('http://localhost:8000/')
  .then(r => r.text())
  .then(console.log)
```
At this point you may encounter ***CORS*** errors. Configuring our ***cross-origin resource sharing*** is an important step if we want to allow access to our server from different 'origins'. We can get specific about exactly where we will allow access from, but for now let's allow anyone in. In our `http` server, we can do this by setting our headers.
```js
// in server.js
const requestListener = (request, response) => {
  response.setHeader('Access-Control-Allow-Origin', '*'); // NEW LINE
  response.writeHead(200);
  response.end('Hello, client!');
};
```

### Returning different data
Right now we're sending just the string 'Hello, client!' back. \
If we want to send other data, we will want to make sure we ***stringify*** it first. This is a built in method for the [JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) object. If in doubt, JSON.stringify your data, even if it's merely a string for consistency.
```js
const data = { stuff: 'this', nonsense: 'that'}
res.end(JSON.stringify(data));
```
In this case, to 'translate' it on the client-side, let's use the `.json()` method instead of `.text`
```js
// client-side js
fetch('http://localhost:8000/')
  .then(r => r.json()) // Try it with .text() then .json() - what do they each provide?
  .then(console.log)
```
### Adding Routes
At the moment, no matter what request we send, we get the same response. Let's spice things up a bit and return different data depending on the request that is made. The `http` module doesn't offer much in the way of fancy solutions here so let's check exactly what path was given with a good old `switch` statement. We are checking the slightly misleadingly name `url` property of the request object. Generally a ***url*** is the full address eg. `http://localhost:8000/cats` whilst the ***path*** is just the part after the root domain eg. `/cats` so just a heads up that this is slightly strange naming courtesy of the `http` module! You can use any logic you want to put it all together, as long as you are, at some point, wrapping up with a `response.end(dataYouWantToSendBack)`. Here's what I've put together:
```js
const requestListener = (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.statusCode = 200;
    let body;

    switch(req.url){
        case '/':     // http://localhost:8000/
            body = 'Hello, client!'; break;
        case '/bob':  // http://localhost:8000/bob
            body = 'Hello, Bob!'; break;
        case '/cats': // http://localhost:8000/cats
            body = { cats: [{ name: 'Zelda', age: 3}, { name: 'Tiger Lily', age: 10 }] }; break;
        default:      // any non-matching routes
            res.statusCode = 404;
            body = { error: 'Unknown route' };
    }

    res.end(JSON.stringify(body));
};
```

### Query Parameters
It would be cool if instead of a `/bob` route, we could have a `/greeting` route that could customise the response with any given name! One way we could send this name info across to our server might be with query parameters eg. `http://localhost:8000/greeting?name=Beth`.
#### Custom params parsing
So how to parse this when received by the server? We could do it manually by splitting the incoming `req.url` string at the `?` and making the appropriate adjustments to our switch eg.
```js
const requestListener = (req, res) => {
    const [path, queryString] = req.url.split('?') // splitting req.url and destructuring the return value out to two new variables
    // etc...
    let body;

    switch(path){ // checking new path variable instead of req.url
        case '/':
            body = 'Hello, client!'; break;
        case '/greeting': // if queryString is truthy, split it at the '=' and take the second item
            const name = queryString && queryString.split('=')[1]; 
            body = `Hello, ${name}!`; break;
        // etc...
    }

    res.end(JSON.stringify(body));
};

```
What if we have more than one query parameter? eg. `http://localhost:8000/greeting?name=Beth&location=London` \
We could make a custom helper?
```js
function getPathAndParams(req){
    const [path, queryString] = req.url.split('?');
    const params = {};
    if(queryString){
        const paramPairs = queryString.split('&');
        paramPairs.forEach(p => {
            const [key, value] = p.split('=');
            params[key] = value;
        })
    }
    return [path, params];
}

const [ path, params ] = getPathAndParams(req);
```
#### Built-in params parsing
Alternatively you could use node's `url` module (nope, that's not the same as `req.url`, yes, that's a bit confusing!), or another 3rd party library. This custom option above is more for academic study of what exactly we want to do when we say 'parse query parameters'. Use it to help you when reading documentation for any 3rd party solution you may be considering.

### More request methods
So far we've been making 'get' requests to our server, although our server is not actually checking to see what method is being used yet and it certainly can't re-route based on method type. \
This is easily added by checking the response object's `.method` property.

Let's say we want to have two routes with the endpoint of `/cats`. If we make a `GET` request, we receive the cats back. If we make a `POST` request, with a body of new cat data, we can add the new cat to our collection of cats.
```js
case '/cats':
  if (req.method === 'GET'){
      // deliver cats
  } else if (req.method === 'POST') {
      // add new cat
  }; break;
```

Getting the body out of a request is a bit tricky with the `http` module. We are grabbing chunks of data as they come in and then parsing them when the request has delivered all its content.
```js
// in server js requestListener switch statement
case '/cats':
  if (req.method === 'GET'){
      body = { cats: allCats };
  } else if (req.method === 'POST') {
      let reqBody = [] // a place to gather data chunks as they come in
      let newCat; // declaring the concept of a new cat
      req.on('data', chunk => reqBody.push(chunk)); // listening for new data chunks and storing them in our reqBody array
      req.on('end', () => { // once the request has finished delivering all its data
          newCat = JSON.parse(reqBody); // parse the collected data chunks and store them in variable
          allCats.push(newCat); // push the parsed data to our allCats collection
      })
  }; break;
```

Now we can make a GET request to the `/cats` endpoint from the client:
```js
// in client-side js
function submitCat(e){
    e.preventDefault();

    const catData = {
        name: e.target.name.value,
        age: e.target.age.value
    };

    const options = { 
        method: 'POST',
        body: JSON.stringify(catData)
    };

    fetch('http://localhost:8000/cats', options)
        .then(r => r.json())
        .then(() => appendCat(catData))
        .catch(console.warn)
};
```

But we can also still make a GET request to the same path!
```js
function getAllCats(){
    fetch('http://localhost:8000/cats')
        .then(r => r.json())
        .then(appendCats)
        .catch(console.warn)
};
```