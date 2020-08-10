## Module Bundlers

As our projects get bigger and more complex, it’s often useful to use a module bundler to automate a lot of tasks for us, especially around deployment.

Bundlers allow us to import code from files and folders into other files and folders.

An example of this would be moving development code into a `dist` folder which only contains the code you want to deploy and leaving out folders/files such as tests which could be a security risk.

## Webpack

Webpack is a popular open-source JavaScript module bundler, able to copy static files, transpile ES6 to ES5 and more.

## Setup

To use Webpack we need to go through some setup.

Begin by creating a project repository and running the standard `git init` and `npm init`.

### Install Webpack

`npm install webpack webpack-cli webpack-dev-server html-webpack-plugin --save-dev`

We use the `--save-dev` as we only need these for development and not production

### Install Loaders

We use these to add support for various file types.

`npm install babel-loader style-loader css-loader sass-loader node-sass --save-dev`

### Install Babel

We use this to tanspile ES6 to ES5.

`npm install @babel/core @babel/preset-env @babel/preset-react`

### Folders and FIles

To configure babel, we need to create a file called `.babelrc` and write in the following:

```json
{
    "presets":[
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
```

We also need to create an `src` and a `public` folder to store our source code and assets.

Our next task is to instruct Webpack on how it should work by creating a `webpack.config.js` file and inserting the following:

```js
const path = require('path')
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/index.js', // the main JavaScript file of the app/project
  output: { // instructions for compiling the code
    path: path.resolve('dist'), // the file where the compiled code should go
    filename: 'bundle.js', // the file name of the compiled code
    publicPath: '/' // redirect incoming requests to '/'
  },
  devtool: 'source-maps', // a tool to find errors in the compiled code, but show them against the source code for easier debugging
  module: { // modules/helpers we want Webpack to use
    rules: [ // instructions for the modules/helpers
      { test: /\.jsx?$/, loader: 'babel-loader', exclude: /node_modules/ }, // transpile JSX files
      { test: /\.css$/, loader: ['style-loader', 'css-loader'] }, // transpile css files
      { test: /\.s(a|c)ss$/, loader: ['style-loader', 'css-loader', 'sass-loader'] } // transpile sass/scss files
    ]
  },
  devServer: { // instructions for the development server
    contentBase: path.resolve('src'), // location of the source code
    hot: true, // refresh the browser when changes are saved
    open: true, // open the app/project in the browser when the server starts
    port: 8000, // use this port for the server
    host: '0.0.0.0', // server is accessible externally
    historyApiFallback: true, //serve a previous page on a 404 error
    watchContentBase: true // watch for changes to static files
  },
  plugins: [ // plugins we are using
    new webpack.HotModuleReplacementPlugin(), // update changed modules without page reload
    new HtmlWebpackPlugin({ // add JavaScript code to the HTML
      template: 'public/index.html',
      filename: 'index.html',
      inject: 'body'
    })
  ]
}
```

Finally we should add some scripts to our `package.json`.

```json
"start": "webpack-dev-server --mode-development",
"build": "webpack -p"
```