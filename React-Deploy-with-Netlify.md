Time to deploy your React app? Let's walkthrough setting up for a Netlify deploy.

## .toml file
To configure a Netlify deployment, you can use the UI but today we will create a configuration file in our project instead. The netlify.toml file is going to contain the information Netlify needs to build and deploy our app. Create it in the top level of your project folder. 
- `touch netlify.toml` _(Mac/Linux/GitBash)_
- `New-Item netlify.toml -type file` _(Powershell)_

***NB: If you use the UI for configuration, you will still need to define the command and publish settings but the UI will walk you through this***

## Build
Whatever you use to deploy your React app, you'll need to build it first. Netlify can do this for us if we give it the right information in the toml file.
```toml
# in netlify.toml
[build]
    command = "npm run build" # how to trigger a build
    publish = "/dist" # what folder to publish 
```
If you're not sure what the name of your publish folder is, run your build command and see the name of the folder it creates. You can customise this in a webpack.config.js in output.path.
```js
// in webpack.config.js
output: { 
    path: path.resolve(__dirname, './dist'), // here!
    // ..etc
},
```

## Handle redirects
As we only have one html file, we want to make sure that our users are redirected to our single `index.html` regardless of the url. It will be up to React to handle routing from there.
```toml
# in netlify.toml
[[redirects]]
    from = "/*" # redirect any path
    to = "/index.html" # to this html page
    status = 200 # with this status
```

## Deploy
Now it's business as usual for a Netlify deploy! \
Check the [Netlify Deployment 101](https://github.com/getfutureproof/fp_guides_wiki/wiki/Deploy-101) for the next steps!