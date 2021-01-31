There are numerous ways to deploy a full stack application, in this guide we will take a look at just a few, using [Netlify](https://www.netlify.com/) and [Heroku](https://www.heroku.com/).

## Option 1 - Split the Client and Server

If you decide to go down this route before commencing on the building of your app, you can simply create separate git repositories. Once ready to deploy you can host your front-end on Netlify and the back-end to Heroku using these guides:

- [Static Site Deployment with Netlify](https://github.com/getfutureproof/fp_guides_wiki/wiki/Deploy-101)
- [Deploying an Express API to Heroku](https://github.com/getfutureproof/fp_guides_wiki/wiki/Deploying-an-Express-API-to-Heroku)

_Note: Once you deploy your server and before you deploy your client, remember to change any request urls to point to your Heroku app's endpoints rather than `localhost`._

If you did not make the decision to host the front-end and back-end separately before building your app and now have one combined repository, don't worry, there are only a few additional steps.

Firstly you will need to uninitialize the git repository by navigating to the root of the project and running the delete command `rm -rf .git`.