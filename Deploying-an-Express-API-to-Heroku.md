Heroku is a popular platform for deploying serverside (or full stack) applications quickly, easily and, if you don't mind the restrictions, for free.

#### Heroku vs. Netlify
Netlify is designed for JAMStack applications. JAMStack stands for JavaScript, APIs and Markup. The defining feature of a JAMStack application is that it does not rely on server-side code. We can make sites with the 'languages of the web' HTML, CSS and Javascript and also make calls out to APIs. What we cannot do is host an API (well, you kind of can but that's a story for another day!), database, server-side files etc.

Server activity costs money which is why there isn't such a variety of free options and why Heroku is so popular! The initial main caveat of the free tier is that your server will wind down to sleep after 30 minutes of inactivity. When sleeping, the server will take up to 30 seconds to wake up again when next called.

## Let's deploy an Express API
### Setup 
Once you've [signed up](https://signup.heroku.com/) for Heroku, navigate to your [apps dashboard](https://dashboard.heroku.com/apps) and click 'Create new app'. When asked for the region, choose the one that is physically closest to where you anticipate the majority of your visitors to be coming from. If that's a tough call, go for the one closest to you. For now, we won't add any pipeline but this is a very interesting and useful topic so do check out the documentation [here](https://devcenter.heroku.com/articles/pipelines).

Once your Heroku app is initialised, you have some options on how to proceed. The instructions are easy to follow and we won't go through step by step. Let's use the GitHub integration for now.

#### GitHub integration
Once you have authorized Heroku to connect with your GitHub, search for your repo and hit 'Connect'. If you go for automatic deploys, you'll want to consider carefully here what branch you deploy and create a new one specifically for production deploys if you like.

#### Heroku CLI (Optional)
Even if deploying via GitHub, you're probably going to want the Heroku CLI soon enough. You can find install options for your OS [here](https://devcenter.heroku.com/articles/heroku-cli). Once installed, make sure to authenticate with `heroku login` (add the `-i` flag to avoid opening your browser to login).

### Preparing Your App
There's a good chance that by now you've deployed something but it's not working. The best place to check out what's going wrong is in your logs which you can access via the Heroku CLI. Let's say you want to check the logs for an app you've created called `fp-express-cats`. Even if you created this in the UI and with GitHub integration, you can check the logs with:
```bash
heroku logs --tail -a fp-express cats
```
If you're not sure what apps you have access to or their names, run `heroku apps` \
If you're not interested in previous log output, you can drop the `--tail` flag.

Your logs will provide great information including [error codes](https://devcenter.heroku.com/articles/error-codes) to debug your issues.

Here are a few things specific to our express server to make sure you add/update:

#### Add a Procfile
Your [Procfile](https://devcenter.heroku.com/articles/procfile) tells Heroku 'what to do'. You may get away without doing this but let's make a basic one so we have control. You can always add to it. In this case, we need to let it know how to actually start my Express server. We will declare it as a `web` process:
``` js
// in Procfile
web: npm start
```

#### Check your dependencies
Heroku will find your package-json.lock and build dependencies for you based on that file. It will not include any dev dependencies so make sure, for example, that your Procfile does not rely on any dev dependencies. `nodemon` is a popular tool in node app development. If you are using it, your package.json scripts might end up looking something like this:
```js
"scripts": {
    "start": "node server.js",
    "start-dev": "nodemon server.js"
},
```

#### Check your port
You may well have hard coded your Express port. Heroku will ignore this and use its own environment variable so make sure your app is ready for this with something like:
```js
const port = process.env.PORT || 3000; // if there is no PORT env variable, 3000 will be used
server.listen(port, () => console.log(`Express is running on port ${port}`))
```
We do not necessarily have to know this port number once deployed as you will be able to visit your apps's heroku address without declaring the port.

#### Remove any unnecessary files that don't need to be deployed by adding them to your `.gitignore`. This includes items like node modules, development docker config, etc.

If they have already been added to your git history you can remove them with the command `git rm -r --cached .`

## Accessing your API
You can now access your API at the Heroku domain eg `https://fp-express-cats.herokuapp.com/` and your endpoints as expected eg `https://fp-express-cats.herokuapp.com/cats/zelda` 

We've deployed this without any client side code so we are only getting the raw data being sent back from our API. You could host your whole app on the same Heroku dyno but given the limitations, it might be better to host your client side separately. Wherever you host your client, make sure your fetch requests are pointing to your new Heroku domain!