There are numerous ways to deploy a full stack application, in this guide we will take a look at just a one, using [Netlify](https://www.netlify.com/) and [Heroku](https://www.heroku.com/).

## Split the Client and Server

If you decide to go down this route before commencing on the building of your app, you can simply create separate git repositories. Once ready to deploy you can host your front-end on Netlify and the back-end to Heroku using these guides:

- [Static Site Deployment with Netlify](https://github.com/getfutureproof/fp_guides_wiki/wiki/Deploy-101)
- [Deploying an Express API to Heroku](https://github.com/getfutureproof/fp_guides_wiki/wiki/Deploying-an-Express-API-to-Heroku)

_Note: Once you deploy your server and before you deploy your client, remember to change any request urls to point to your Heroku app's endpoints rather than `localhost`._

If you did not make the decision to host the front-end and back-end separately before building your app and now have one combined repository, don't worry, there are only a few additional steps.

Firstly you will need to uninitialize the git repository by navigating to the root of the project and running `rm -rf .git`.

If your code is not already in two folders, split the front-end and back-end code so you filetree looks something like:

```markdown
- App
    - Client
    - Server
```

Next you should make both your front-end and back-end folders git repositories by navigating into them and running `git init`.

_Note: If your app repository was connected to GitHub, this connection will have been severed and so it might be worth setting up GitHub repositories for your new front-end and back-end projects._

Make an initial commit, then you can simply follow the guides mentioned above to deploy the separate parts of your app.

## Adding a Postgres Database

Deploying a database with Postgres is fairly straight-forward with the [Postgres add-on](https://www.heroku.com/postgres).

The first thing you'll need to do is provision your database by running `heroku addons:create heroku-postgresql:hobby-dev` in the root of your project. This will give you access to your database with the config variable `DATABASE_URL`.

You'll then need to add your new database to your app setup, which should look something like the below.

```js
const { Client } = require('pg');

const client = new Client({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});

client.connect();
```

If you need to run any migration or seed files, you will need to do so manually by navigating to the folder with this code and running `cat <file_name> | heroku pg:psql`