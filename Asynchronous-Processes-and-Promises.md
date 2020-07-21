## 'Asynchronous JavaScript'
JavaScript in and of itself, is a ***synchronous*** language, however there are ways we can make it behave in an asynchronous manner.

### Callstack
We can only actually execute one thing at a time. Whenever JavaScript is asked to do something, the request is pushed onto the bottom of the ***callstack***. The way that JavaScript decides what to run next is by looking at the top of ***callstack*** and taking the first item. When we talk about the ***stack***, this is what we're talking about.

### The Problem: blocking
We want to avoid 'blocking' the stack with slow code. This is especially important when we want our users to be able to continue interacting with our application. Imagine we triggered a very slow process and our user had to wait until it was done before they did anything else? They might start clicking around, adding things to the callstack, and finally when our slow process is done, everything else will catch up very quickly. This is similar to when your computer freezes, you're clicking around trying to interact, nothing happens, and suddenly it ALL happens and you end up with a big mess. We definately don't want our users to experience anything like this!

### The Solution: WebAPIs (in-browser) / C++ APIs (server-side)
In order to solve this, we can outsource our slow processes to APIs which are running independently of the callstack. It takes the request off the top of the callstack and gives it to the relevant API to deal with. This allows us to get on with other things on our callstack. Once that process is done, it will pass its resolution (often a callback function of some kind) to the ***task queue***.

### Event Loop
The event loop monitors the ***callstack*** and the ***task queue***. Only when the callstack is empty will the event loop grab the first item in the task queue and push it onto the callstack.

**I *strongly* recommend several views of the excellent talk [What the heck is the event loop anyway?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)**

***

## What is a Promise?
Let's say you've ordered some food and you've been given one of those buzzers. You holding that buzzer means that someone has made you a promise that food will be available, not quite yet, but hopefully soon. Since you have this buzzer, you can go off and do other things if you like - make a phone call, use the bathroom etc. During this period, the promise is ***pending***

When the buzzer goes off, you'll finish up whatever you're doing and then you'll go and collect you food. \
If your food is there waiting for you, the promise has been ***fulfilled*** successfully.

If your food is missing and instead you get some half-baked apology about how they've actually just realised that they've run out of the unicorn tears required to make your dish, the promise has been ***rejected*** and has failed.

These three states - `pending`, `fulfilled` and `rejected` - are also used by [JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).

Another analogy for Promises that works very well is laid out ['here, with code examples](https://scotch.io/tutorials/javascript-promises-for-dummies).

### Pre-made functions
There are various pre-made functions that return a Promise. Two that we have used so far (and will continue to use) are [fetch()](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) and [json()](https://developer.mozilla.org/en-US/docs/Web/API/Body/json)

### Make Your Own
You can make your own functions that return Promises too! When we create a new Promise object, we pass it a function which receives a resolution function and a rejection function. Depending on what we decide in the body of our callback, we can decide which one to invoke and what to pass it, therefore coming to the end of the Promise.
```js
const orderBurger = () => new Promise(burgerUp); // orderBurger is returning a Promise object

function burgerUp(resolve, reject){ // As I'm passing it to a Promise constructor, burgerUp will receive 2 functions when it is invoked
  if(specialSauceSupply > 0){
    const burger = { burger: ["portobello", "lettuce", "tomato", "special sauce"]};
    resolve(burger); // If we have enough special sauce, we will deliver the burger
  } else {
    const error = "We are out of special sauce..";
    reject(error); // If we're out of special sauce, we will send a message saying why we couldn't fulfill the promise
  }
};
```

### Ways to handle promise states
#### then / catch
We've seen before that we can chain `.then` and `.catch` to handle the possible results of a Promise.
```js
orderBurger()      // I order the burger
  .then(eat)       // If the Promise is fulfilled, I shall eat the burger
  .catch(goHungry) // If the Promise is rejected, I shall have to go hungry!
```

#### async / await
Another way to go about this is with `async` and `await`. These keywords work in tandem. You can only use `await` inside a function which you have declared to be `async`.
```js
async function hungry(){ // Note the placement of the async keyword
  try {
    const burger = await orderBurger(); // I'll await the result of my order and deconstruct the object
    eat(burger); // If all went well (the Promise was fulfilled), I'll eat my burger
  } catch (err) {
    goHungry() // If something went wrong (the Promise was rejected), Ill have to go hungry
  }
}
```

For an arrow function, the `async` keyword goes right before the parameters:
```js
const hungry = async () => { // Note the placement of the async keyword
  // same body as above
};
```

### Put them to use 
Let's see both of those in use with `fetch` and `json`, which is likely to be a common flow you will be implementing. \
Async/Await is a newer addition and it is very common to see now but it's worth being comfortable with and understanding both.
```js
const url = "https://api.github.com/users";

// Using .then chaining
function getGitHubData(username){ // The function does not need the async keyword
  fetch(`${url}/${username}`) // We call `fetch` which we know returns a Promise
    .then(resp => resp.json()) // If the fetch Promise was fulfilled, we will call `json` which also returns a Promise
    .then(jsonData => console.log(jsonData.location)) // If the json Promise was fulfilled, we will do something with the data
    .catch(console.warn) // If anything went wrong during this chain, we will print a warning in the console
};
 
// Using async/await
async function getGitHubData(username){ // The function needs the async keyword
  try { // The try/catch blocks are not async/await specific but are very useful for handling rejections and Errors neatly
    const resp = await fetch(`${url}/${username}`); // We call `fetch` which we know returns a Promise and when it is fulfilled, store the Response.
    const jsonData = await resp.json(); // Once we have resp, we will call `json` on it. When that is fulfilled we will store the resulting json data.
    console.log(jsonData.location); // Once we have jsonData, we will log out a piece of it
  } catch (err) {
    console.warn(err) // If anything went wrong during the try block, we will print a warning in the console
  }
};
```