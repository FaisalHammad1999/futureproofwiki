When creating your own API, you'll want to add in some tests to check that your endpoints are responding in the way you intend when they **receive** a request.

_Note that for testing code that **makes** requests, check out our [Mocking Modules for Testing with Jest](https://github.com/getfutureproof/fp_guides_wiki/wiki/Testing-fetch-with-Jest) guide._

For this, we can make use of a library such as [supertest](https://www.npmjs.com/package/supertest). It can be used with any test runner. The examples below are done with [Jest](https://github.com/getfutureproof/fp_guides_wiki/wiki/JS-Unit-Testing-with-Jest) in mind but the majority of the code would be exactly the same if you were using eg. [Mocha](https://github.com/getfutureproof/fp_guides_wiki/wiki/JS-Unit-Testing-with-Mocha-and-Chai).

## Install supertest
`npm install -D supertest` \
Supertest is available as an npm library. The `-D` flag is an alternative for `--save-dev` and either is valid. We only need supertest during development so installing it as a dev dependency is appropriate here.

## Require supertest
In the test file you wish to use it (or in a test setup file), require the library. You can name it anything you wish but the convention is to call it `request`.
```js
const request = require("supertest")
```

## Let the tests see your server
We'll need access to our server so require it giving any name you like (here we've used 'server') and point it to a file which exports your server (in our case that's `../server` since the file is called `server.js` and lives in the folder above) 
```js
const server = require("../server")
```

Note that to avoid the server running as soon as you require it (not desired!), it is best practice to export the server from where it is defined and import it into another file to be run at a suitable time. Your file structure may look something like this:
```bash
|- server
    |- index.js # server is imported into here and started when this file is run
    |- server.js # server is defined and exported here
    |- /test
        |- api.test.js # server is imported into here and starts with test run
```

## Setup your tests
As with any test suite, you need to explain what you are testing and do any setup. In this case we are testing our API endpoints. Before we run the tests we will need to start up the API server and after them, we will need to stop it. In Jest we have the `beforeAll`, `beforeEach`, `afterAll` and `afterEach` hooks as options here. Other test runners will have similar options.

As we are now working with actions that take some time, we are also going to make use of an argument that Jest (and other test runners) will pass to callback functions. For convention's sake, we will call it `done`. This `done` argument is another function that, when invoked, indicates to the test runner that it is okay to move on. If you get a `Timeout` error in your test suite, check that you have stated when `done` should be run before looking into other solutions.

```js
const request = require("supertest");
const server = require('../server');

describe('API server', () => {
    let api
    let testCat = {
        "name": "Bob",
        "age": 6
    }

    beforeAll(() => {
        // start the server and store it in the api variable
        api = server.listen(5000, () => console.log('Test server running on port 5000'))
    })

    afterAll(done => { // `done` always gets passed in but if we want to use it, we must name it!
        // close the server, then run done
        console.log('Gracefully stopping test server')
        api.close(done) // `done` will be invoked when the `api.close` function is complete
    })
})
```

## Writing an endpoint test
To make a request to your api, you can use one of supertest's handy methods such as `.get` to make a request and `.expect` to make assertions on the response.

### Testing a GET request
```js
test('it responds to get /cats with status 200', done => { // we will want to call `done` when the test is complete so make sure you name it
    request(api) // let supertest know the server it is requesting to (note we assigned our server to the variable `api` in the setup)
        .get('/cats') // perform a get request to the endpoint of '/cats'
        .expect(200, done) // assert that the response status will be 200 and pass `done` so expect can call it when it is... done!
})
````

### Testing a POST request
A POST request usually has some data sent along with it. Supertest allows us to add that into the mix with the `.send` method.
```js
test('it responds to post /cats with status 201 and returns a new cat with an ID', done => {
    let testCat = { "name": "Bob", "age": 6 } // Create some data to pass
    request(api)
        .post('/cats') // start a post request via supertest
        .send(testCat) // send the testCat data as the request body 
        .expect(201) // assert that the response status will be 201
        .expect({id: 4, ...testCat}, done) // assert that the response body will include an object with the passed data and an added ID
})
```

### Testing other methods
Supertest provides support for a host of other methods including `.patch`, `.put` and `.delete`. The implementation is as above.

### Setting and checking headers
Whilst there are many more features of supertest, one that you might need to use more often is handling request headers.

To add a header to a request, use the `.set` method:
```js
test('it responds to post /cats with status 201 and returns a new cat with an ID', done => {
    let testCat = { "name": "Bob", "age": 6 } // Create some data to pass
    request(api)
        .post('/cats') // start a post request via supertest
        .send(testCat) // send the testCat data as the request body 
        .set('Content-Type', 'application/json') // add a header of 'Content-Type' with value 'application/json'
        .expect(201) // assert that the response status will be 201
        .expect({id: 4, ...testCat}, done) // assert that the response body will include an object with the passed data and an added ID
})
```

and to make an assertion on a response header:
```js
test('it responds to get /cats with status 200', done => { 
    request(api) 
        .get('/cats')
        .expect('Content-Type', 'application/json') // assert that the response will have header of 'Content-Type' with the value 'application/json'
        .expect(200, done)
})
````
