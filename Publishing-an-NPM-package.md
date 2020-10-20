We're starting to get cosy with installing npm packages that other people and companies have made using the `npm install <package-name>` command. It would be great to start sharing our own handy little chunks of code with others!

## Signup/login to the npm registry
- `npm adduser`
- update if prompted
- Check your email to login and verify your email address with the npm registry

## Create a folder and git repository for your package
- `mkdir <project-name>`
- `cd <project-name>`
- `git init`
- `git remote add origin <your-repo-url>`

## Initialise an npm package
- `npm init`
- Check out the resulting `package.json` and update as you see fit

Ours looks like this:
```js
{
  "name": "hello-futureproof",
  "version": "1.0.0",
  "description": "futureproof npm package demo",
  "main": "index.js",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/getfutureproof/fp_study_notes_publishing_npm_packages.git"
  },
  "author": "futureproof",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/getfutureproof/fp_study_notes_publishing_npm_packages/issues"
  },
  "homepage": "https://github.com/getfutureproof/fp_study_notes_publishing_npm_packages#readme"
}
```

## Write some code!
- create a file called `index.js` (or something else, as long as you update `"main"` in your `package.json`)
- export a function from `index.js` using `module.exports = <myFunction>` - this is what will run when a user runs your package
    + you can write this as an anonymous function or point to a function defined elsewhere in your code

```js
module.exports = function(name){
    return `Hello from npm, ${name}! Welcome to futureproof!`
}
```

## Set your version
- `npm version 1.0.0`

## Stage, Commit & Push to GitHub
- you know how to do this!
- additionally, run `git push --tags`

## Publish to the npm registry!
- `npm publish`
- That's it! Check out your handiwork at `https://www.npmjs.com/package/<your-package-name>`
- You can test your package in a sandbox at `https://npm.runkit.com/<your-package-name>`. [See example here](https://npm.runkit.com/hello-futureproof).

## Share with others
Others can now install your package to their node project with `npm install <your-package-name>` and use as any other package!
```js
const fp = require('hello-futureproof');

let greeting = fp('Beth')

console.log(greeting)
```

***

For more information including how to make a CLI executable package, check out the [official documentation](https://docs.npmjs.com/packages-and-modules/)

