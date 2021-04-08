## Module Bundlers

One of the key 'problems' we commonly solve with a module bundler is the fact that, unlike Node which has a built in module resolver, most browsers do not have the ability to handle `require` or `import`.

As our projects grow, we usually want to be splitting our code across many different files to aid organisation and human readability. Let's say we have 5 JavaScript files - usually we would just `require` or `import` them into each other as needed. However, if we want to be able to use them in the browser, we would need to give each one a `script` tag in the `html` file. This can get messy quickly so we can use a module bundler to create one large file with all the needed code in and just bring that file into the html.

But wait, there's more! Many bundler tools have been extended to include many other features such as handling more file types, automating additional tasks, generating helper files etc.

An example of this would be moving development code into a `build` folder which only contains the code you want to deploy and leaving out folders/files such as tests which could be a security risk.

## Webpack

[Webpack](https://webpack.js.org/) is a popular open-source JavaScript module bundler, able to copy static files, transpile ES6 to ES5 and more.

## Setup

To use Webpack we need to go through some setup.

Begin by creating a project repository and running the standard `git init` and `npm init`.

### Install Webpack
`npm install webpack webpack-cli webpack-dev-server html-webpack-plugin --save-dev`

We use the `--save-dev` flag as we only need these for development and not production. _(NB: You can shorten `npm install --save-dev` to `npm i -D`)_

### Install Loaders
We use these to add support for various file types.

`npm i -D babel-loader style-loader css-loader`

### Install Babel

We use this to transpile ES6 to ES5. Install the following dependencies:
- `@babel/core`: the core babel library 
- `@babel/preset-env`: allows us to target specific environments
- `@babel/plugin-transform-runtime`: if you get regenerator runtime errors you may have forgotten this one!
- `@babel/plugin-proposal-class-properties`: optional - recommended if you will be using ES6 class syntax
- `@babel/preset-react`: only add if creating an app that uses React

To configure babel, we need to create a file called `.babelrc` and define any presets and plugins that you've installed. If you use all of the ones above, your `.babelrc` will look like this: 

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"],
  "plugins": ["@babel/plugin-proposal-class-properties", "@babel/plugin-transform-runtime"]
}
```

### Folder structure
Folder and file organisation is going to be extra important now. You can configure your setup to your taste but here is a very standard flow:
- Create an `src` folder to work in, creating our source code which may or may not be bundled later
- Create a `public` folder to store the publicly available assets
- Create a `config` folder to store the configuration files stating how your app should be built

### Webpack configuration
These will live in the `config` folder

- Create a `webpack.config.js`
Use the following as a starting point. Read through it and if there's anything you don't quite understand, use your search engine of choice to get more info!
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

const ROOT_DIRECTORY = path.join(__dirname, '../'); // the root of your project
const PUBLIC_DIRECTORY = path.join(ROOT_DIRECTORY, 'public'); // the root of the frontend, i.e. html file

const config = {
  entry: [path.resolve(ROOT_DIRECTORY, 'src/index.js')], // the main JavaScript file of the project
  output: {
    // instructions for compiling the code
    path: path.resolve(ROOT_DIRECTORY, 'build'), // the file where the compiled code should go
    filename: 'bundle.js', // the file name of the compiled code
    publicPath: '/', // specifies the base path for all the assets within your application.
  },
  mode: 'development', // tells webpack to use its built-in optimisations according to the mode
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

We also create a configuration file with extra instructions specifically for use in development environment.

- Create a `webpack.config.dev.js`
Use the following as a starting point. Read through it and if there's anything you don't quite understand, use your search engine of choice to get more info!

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

Thinking ahead we will also need to let webpack know how to build a production ready application by creating a `webpack.config.production.js` file:

```js
const config = require('./webpack.config.js');

config.mode = 'production';
module.exports = config;
```

Finally we should add some scripts to our `package.json`.

```json
  "scripts": {
    "dev": "webpack-cli serve --mode development --config config/webpack.config.dev.js",
    "build": "webpack --config config/webpack.config.production.js"
  },
```

**And we're ready to go! Run the dev script with `npm run dev` and get coding!**
