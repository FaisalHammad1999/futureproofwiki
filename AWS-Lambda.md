AWS Lambda is a serverless function solution. Perfect for if you have a small action you want to make that would normally go server-side but you don't want to build out and deploy a whole server!

---

## Getting Started
- Go to the [Lambda console](https://console.aws.amazon.com/lambda/home)
- Click `Create function`
- Name your function something descriptive and choose your runtime eg. Node.js 14.x
- The default permissions and settings are fine for our purpose today but feel free to take a look!
- Click `Create function` and wait for it...

---

## Dependencies

### Creating layers
To 'install' dependencies, you'll need to add layers. Let's walkthrough making a layer for the `axios` npm module
- In your local environment, create a directory for your new layer eg. `mkdir myLayer` and `cd` into it
- In here, `mkdir nodejs && cd nodejs`
- Run `npm init -y` (inside `nodejs` folder)
- Install your dependency eg. `npm i axios`
- `cd ..` out of your layer directory
- Zip up the layer directory `zip -r myLayer.zip myLayer`

### Uploading new layers
- Click back to the Lambda Dashboard
- In the left hand menu under `Additional Resources` select `Layers`
- Click `Create layer`
- Name your layer and click the `Upload` button
- Choose your runtimes (make sure you include the one your function runs in!)
- Click `Create`

### Adding layers to functions
Now you have a new layer you can use it in any function you create in a compatible runtime, you don't need to make duplicate layers
- In your function's console, scroll to the bottom and click `Add a layer`
- Select `Custom layers` and choose your layer from the dropdown
- Click `Add`

---

## The Function

### Writing your function
In your function's console `Code source`, you can double click on a file name to open it. You'll get a stubbed out lambda handler which is the actual 'lambda function'. This is the entry and exit point of the lambda. The argument it receives will be the trigger event and the return value will be the response object sent back.

Let's walk through making a function that receives a GitHub username, retrieves their data from the GitHub API and returns it back to the original request location.
- Double click the provided `index.js`
- At the top of the file import your dependencies eg. `const axios = require('axios');`
- Use the (imaginary) event body to retrieve this (imaginary) GitHub username
- Make a call to the GitHub API using the received username
- Extract the data you need and send it back in the response
- Add error handling as appropriate!

Here is a basic example:
```js
const axios = require('axios');

exports.handler = async (event) => {
    const body = JSON.parse(event.body)
    const username = body.username
    let response
    
    try {
        const url = `https://api.github.com/users/${username}`
        console.log('url is', url)
        const { data } = await axios.get(url)
        const formatted = {
            whereTheyAre: data.location,
            whoTheyAre: data.name,
            aboutThem: data.bio,
            contact: data.email
        }
        response = {
            headers: { "Access-Control-Allow-Origin": "*" },
            statusCode: 200,
            body: JSON.stringify(formatted),
        };
        
    } catch (err) {
        response = {
            headers: { "Access-Control-Allow-Origin": "*" },
            statusCode: err.response.status,
            body: JSON.stringify(err.message),
        };
    }
    
    return response;
};
```

### Add environment variables
If you need to use environment variables eg. for API keys etc. this is a perfect way to get them off your client if you don't want to make a complete back end server.
- In your function's console, go to the `Configuration` tab
- From the left hand menu select `Environment variables`
- Click `Edit` and add your key value pair eg. key of `MY_API_KEY`, value of `super-secret`
- Click `Save`
- To use your API key in your function, use as a normal env var eg. `process.env.MY_API_KEY`

---

## Test

### Test your function
If working in the AWS browser console, you can still test your function
- Click `Test`
- The `hello-world` event template is fine for testing our function above
- Add sample data in the structure you expect (see below for examples to use with our GitHub function)
- Click `Create`
- In `Test` select the test event you want to send and check the result!
- Note if you are using `JSON.parse` for the event body, remove it for test only eg. above would be `const body = event.body`

```js
// event name `ghsuccess`
{
  "body": {
    "username": "notausernaoeusaeuhasetuh"
  }
}

// event name `ghbaduser`
{
  "body": {
    "username": "notausernaoeusaeuhasetuh"
  }
}
```

---

## Use!

### Trigger your function
There are many ways a function can be triggered! We will walk through setting up the ability to call it via an API gateway. This is a great way to do small actions you would normally do server side but for whatever reason you do not want to create an server. Think hiding secret keys in a React app without building out a full Express API for example!
- In your function's `Function overview`, click `+ Add trigger`

**Create API Gateway**
- Select ther trigger of your choice, we will use API Gateway
- In the next dropdown select `Create an API`
- Select the protocol of your choice, REST is good for us here
- Select the security of your choice, `Open` is fine for us here
- The additional settings defaults are fine for our example but check them out to see the options and the note on CORS!
- Click `Add` and you will be redirected to your Triggers list
- Expand the `Details` for your API Gateway and copy the API endpoint, you'll need it to call your function!

**Enable CORS** \
To enable CORS on our new REST API:
- Click on the name of your API Gateway in your triggers list, new tab will open with the API  settings
- Click the name of your function in the Resources tree, click the `Actions` button and select `Enable CORS` 
- Select `DEFAULT 4XX` and `DEFAULT 5XX`, leave the other settings as they are
- Click `Enable CORS and replace existing CORS headers` and confirm `Yes, replace existing values`
- Click `Actions` again and `Deploy API`
- Select `default` for deployment stage or create a new one and you can add a description if you like!
- Click `Deploy`!
- Finally, make sure your response objects have the Access-Control-Allow-Origin header (see example code above)

**Call your function from your app** \
Now you can access you function as you would any other API eg.
```js
async function getUserData(username){
    const body = JSON.stringify({ username })
    const resp = await fetch('https://k44rv0xgu7.execute-api.eu-west-2.amazonaws.com/default/lambda-demo', { method: 'POST', body })
    const data = await resp.json()
    console.log(data);
}

getUserData('getfutureproof')
```

---

## Further Reading
Once you are comfortable creating lambdas in the browser AWS console, have a go at [deploying a lambda using the AWS CLI](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-package.html)

You might have noticed that a POST request felt a bit off for our use above. For a guide on using query params instead of a body, see the [official documentation](https://aws.amazon.com/premiumsupport/knowledge-center/pass-api-gateway-rest-api-parameters/) 
