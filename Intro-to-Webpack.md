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

`npm install babel-loader style-loader css-loader --save-dev`

### Install Babel

We use this to tanspile ES6 to ES5.

`npm install @babel/core @babel/preset-env @babel/preset-react`

This plugin transforms static class properties as well as properties declared with the property initializer syntax.

`npm install @babel/plugin-proposal-class-properties --save-dev`

### Folders and FIles

To configure babel, we need to create a file called `.babelrc` and write in the following:

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"],
  "plugins": [["@babel/plugin-proposal-class-properties"]]
}
```

We also need to create an `src` and a `public` folder to store our source code and assets.

Our next task is to instruct Webpack on how it should work by creating a `webpack.config.js` file and inserting the following:

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

const ROOT_DIRECTORY = path.join(__dirname, './'); // the root of your project
const PUBLIC_DIRECTORY = path.join(ROOT_DIRECTORY, 'public'); // the root of the frontend, i.e. html file

const config = {
  entry: [path.resolve(__dirname, './src/index.js')], // the main JavaScript file of the project
  output: {
    // instructions for compiling the code
    path: path.resolve(__dirname, './dist'), // the file where the compiled code should go
    filename: 'bundle.js', // the file name of the compiled code
    publicPath: '/', // specifies the base path for all the assets within your application.
  },
  mode: 'development', // tells webpack to use its built-in optimizations according to the mode
  resolve: {
    // instructions on how to resolve modules
    modules: [path.resolve('node_modules'), 'node_modules'], // tells webpack where to look for node_modules
  },
  performance: {
    // notifies you if assets and entry points exceed a specific file limit
    hints: false,
  },
  plugins: [
    // plugins we are using to help with compiling
    new HtmlWebpackPlugin({
      // used to add the JavaScript code to the HTML
      template: path.join(PUBLIC_DIRECTORY, 'index.html'),
    }),
  ],
  module: {
    // helpers we want webpack to use
    rules: [
      // specific instructions for each helper
      { test: /\.js$/, exclude: /node_modules/, loader: 'babel-loader' }, // transpile JavaScript files
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      }, // transpile css files
      {
        test: /\.(png|svg|jpg|gif|pdf)$/,
        use: ['file-loader'],
      }, // transpile image files
    ],
  },
};

module.exports = config;
```

We also need to configure a development configuration file. Begin by making a file called `webpack.config.dev.js` and then add the below:

```js
const path = require('path');
const config = require('./webpack.config.js');

config.devServer = {
  historyApiFallback: true, //serve a previous page on a 404 error
  contentBase: path.resolve('src'), // location of the source code
  port: 8080, // use this port for the server
  hot: true, // refresh the browser when changes are saved
  open: true, // open the project in the browser when the server starts
  host: '0.0.0.0', // make server accessible externally
  watchContentBase: true, // watch for changes to static files
};

config.devtool = 'inline-source-map'; // a tool to find errors in the compiled code, but show them against the source code for easier debugging

module.exports = config;
```

Finally we should add some scripts to our `package.json`.

```json
"start": "webpack-cli serve --mode development --config webpack.config.dev.js",
"build": "webpack -p"
```