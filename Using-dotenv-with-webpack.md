[`dotenv`](https://www.npmjs.com/package/dotenv) is a great way to handle our environment variables! An environment variable is something that may want to dynamically change depending on whether we are in a test vs. dev vs. production environment. It may also be something we just want to hide from our actual codebase so it's not published eg. on GitHub - perfect for any API keys we want to keep private!

## Usage in server-side applications
`dotenv`'s own [usage guide](https://github.com/motdotla/dotenv#readme) is quite easy to follow for usage in server-side node applications!

## Usage in client-side applications using webpack
`dotenv` relies on finding the `process` variable which is available in server-side applications, but this does not exist in the browser (much in the same way that we can only access `document` in the browser, not server-side. Luckily, webpack can be configured to convince `dotenv` to work client-side.

### Usage in Create React App - generated apps
Great news, *all* the hard work is done in CRA, it's even already installed! All you need to do is make a `.env` file and access things from it. The only restriction is that you must start all your environment variables with `REACT_APP_`.
```js
// in .env file
REACT_APP_SECRET_GREETING=hello
REACT_APP_MY_API_KEY=e3tu7obonth22o
```

```js
// somewhere in your codebase
<h1>{process.env.REACT_APP_SECRET_GREETING}</h1>
```

### Usage in apps with a custom webpack configuration
We need just a little manual configuration to get this going in webpack ourselves. We're actually going to make our very own webpack plugin!
Add the following to your `webpack.config.js`:
```js
// top level of file:
const webpack = require('webpack');
const dotenv = require('dotenv');

const env = dotenv.config().parsed;

const envKeys = Object.keys(env).reduce((prev, next) => {
  prev[`process.env.${next}`] = JSON.stringify(env[next]);
  return prev;
}, {});

//------------------------------

// within existing config object:
...
  plugins: [
    ...
    new webpack.DefinePlugin(envKeys)
  ],
...
```

Now you can create a `.env` and refer to its contents as above!
