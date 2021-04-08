## What is React?

React is a Javascript library that we can use to build smooth user interfaces. It was created by Facebook in 2011. Aside from Facebook itself, many web applications are built with React including Instagram, Codeacademy and Khan Academy to name a few. React has a very robust community and that is just one of many reasons why people like it so much.

## How does React work?
A quick look at the [ReactJS](https://reactjs.org/) homepage will let us know that components are a key concept in React. We will use components to make reusable chunks of code that can be lego-blocked together to make an application. React will take our components and create a tree which will be then rendered on a 'virtual DOM'. The DOM is already an abstraction of some given code and the React 'virtual' DOM is another abstraction on top of that. The benefits include some very efficient repainting which can really speed things up. When something changes in the regular DOM, the whole thing gets repainted. React however makes use of a diffing algorithm that decides what needs repainting and what does not. If you are interested in finding out more on what goes on under the hood, check out the [docs](https://reactjs.org/docs/faq-internals.html)

## Pre-setup

There are a few ways to get started with React.

- You can **use [create-react-app](https://create-react-app.dev/docs/getting-started/)** which bootstraps a project and gives us a lot of files (some of which we may not need) but it doesn’t require any configuration, even webpack is ready to go for you!

- You can also simply **import React into your website via script tags**, as well as Babel for use of JSX but this is neither a common nor recommended solution.
```html
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

```

- For this lesson, we are going to **create our React app from scratch**.

To go through this lesson though you should have already created a Webpack setup for your project.

## Install

We can install React using `npm install react react-dom`.

We should already have the following files and folders:

- **`package.json`** a manifest file stating, among other things, the dependencies for your app
- **`src`**: a folder that contains the source code for your app
- **`public`**: a folder that contains publicly accessible files
- **.gitignore**

## Setup

We first want to create an `index.html` file in our `public` folder.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Intro to React and Webpack</title>
  </head>
  <body>
    <div id="root"></div>
    <noscript>Please enable JavaScript for the full experience!</noscript>
  </body>
</html>
```

We don't need a script tag or stylesheet links as Webpack will connect it for us.

---

Next we need to create an `index.js` file in our `src` folder.

```js
// index.js
import React from 'react'
import ReactDOM from 'react-dom'

ReactDOM.render(
  <h1>Hello React!</h1>,
  document.getElementById('root')
)
```

The inside of the render block looks a lot like HTML, but it's actually JSX, a language that enables us to mix HTML and JavaScript.

The `ReactDOM.render` line is the entry point of our app. This is where React retrieves the App component we have built and replaces it with the generated HTML.


## Components

Now we have completed the setup, we can begin to separate our code into components. You can use **class components** or **functional components**. It used to be that we needed to use class components for any complex functionality but with the dawn of React Hooks, we can now get the same result with both. The implementation however is very different and you cannot use hooks in class components - instead you use component lifecycle methods. We will be focusing on functional components and React Hooks during the course. Whilst this is by far the most common approach, you will inevitably find class components in documentation, forums and legacy code - we have materials and guides available for class components too if you need it.

First we need to create `App.js` inside of the `src` folder.
Note that the `App.js` is capitalised - this is a React convention when writing components to differentiate from html elements.

```js
// App.js
import React from 'react'

const App = () => <h1>Hello World!</h1>

export default App
```

Now that the code has been moved over, we can import and insert it into our html.

```js
// index.js
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

As you can see below, components can be rendered inside of other components. 

```js
// App.js
import React, { Component } from 'react';
import Greeting from './components/Greeting';
import Jokes from './components/Jokes';

function App(){
    return (
        <>
        <Greeting />
        <Jokes />
        </>
    )
}

export default App;
```
 
We need to export the App so it can be used by the `index.js`. Make sure you only have one parent element within the `return` or you will get an error. You can use fragments `<></>` as the parent element if you wish.

## JSX

We can create variables and use the JSX to display the values.

```js
const Greeting = () => {
    let phrase = "Hello";

    return (
        <h2 id="greeting">
            We just called to say to say {phrase}!
        </h2>
    )
}
```

This is useful as it means we can constantly adapt and update our frontend based on data.


## Working with duplicate elements

Another reason a lot of people like React is that it is declarative. That means that we tell React what we want and it figures out how to do it. Other systems we’ve seen are imperative, meaning we have to tell it how to do each thing.

In order for React to reach its full potential so that the virtual DOM can identify each element and therefore only update the required one we can give each element a key, which should be unique. Keep an eye in your dev tools console for useful warnings letting you know if you've forgotten these!

```js
<ul>
    <li key="item-1">This</li>
    <li key="item-2">That</li>
    <li key="item-3">The Other</li>
</ul>
```

---

## React Dev Tools
The React Dev Tools browser extension is an essential piece of kit when developing React applications! It is available for both [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en) and [Firefox](https://addons.mozilla.org/en-GB/firefox/addon/react-devtools/). After adding the extension you may need to restart your browser. Once installed, your dev tools will have two new tabs: Components and Profiler.

Most of the time you will be bouncing in and out of Compenents along with your usual dev tools tabs. This is where you can view your React tree and inspect them.

The Profiler can be used to give feedback on performance.