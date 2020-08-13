The React documentation on Hooks is excellent and we encourage you to work through this short but thorough intro and guide:
- [Introducing Hooks](https://reactjs.org/docs/hooks-intro.html)

## Why?
Until Hooks, functional components could not have a concept of state nor ability to handle side effects. This can lead to unnecessarily bloated code and, when using Higher Order Components, 'wrapper hell'! Hooks are an OPTIONAL feature of React that are worth getting to know. **HOOKS ARE NOT A REPLACEMENT FOR CLASS COMPONENTS!**

_I recommend a cup of tea and a sit down with Sophie and Dan to get you up to speed on why Hooks were created:_

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/dpw9EHDh2bM/0.jpg)](https://www.youtube.com/watch?v=dpw9EHDh2bM)


## Two Common React Hooks
#### useState
[`useState`](https://reactjs.org/docs/hooks-state.html) is a hook that returns a 'getter' and a 'setter'. To access them both, use array destructuring:
```js
const [ thing, setThing ] = useState();
```
You can call useState with a value to set initial state if you like:
```js
const [ thing, setThing ] = useState('spoon');
```
To access this piece of state, use the getter:
``` jsx
<h1>{thing}<h1>
```
To update this piece of state, use the setter:
``` jsx
<button onClick={() => setThing('fork')}>Change to fork<button>
```

This is similar to using `this.state.thing` and `this.setState({ thing: 'fork'})` in a class component. Just like when using `this.setState`, be careful when referencing the piece of state itself in the update. You can still pass a function instead if you need this access.
```js
const [ lives, setLives ] = useState(60);

const loseALife = () => setLives(prevLives => prevLives - 1);
```
Read [the documentation](https://reactjs.org/docs/hooks-state.html) for more details on the inner workings, things to look out for and more examples.
***
#### useEffect
Functional components do not have access to class Component lifecycle methods. We can, however, achieve some similar behaviour with [`useEffect`](https://reactjs.org/docs/hooks-effect.html) which allows us to handle side effects.

`useEffect` hooks take two arguments - a function (the 'effect') and an optional dependency array. It is important to consider what you need (if anything) in your dependency array. By default, your effect will run everytime a render or re-render is triggered but usually we will only want it to run if something changes. By passing an empty dependency array, the effect will run once only and never again as it has no dependencies. By passing a populated array, the effect will run only if that attribute has changed since the last render.

This runs only once thanks to the empty dependencies array - it is not listening for any changes:
```js
useEffect(() => console.log('Welcome!'), [])
```

This will only run when the `username` is updated:
```js
const [ username, setUsername ] = useState("getfutureproof");

useEffect(() => {
    const fetchUserGists = async () => {
        const resp = await fetch(`https://api.github.com/users/${username}/gists`)
        const gists = await resp.json()
        console.log(gists)
    }
    
    fetchUserGists();

}, [username]); 
```

Another popular side effect is to set an interval or timer of some kind. This behaviour requires 'cleanup'. In a class component you might set an interval in `componentDidMount` and clear it in `componentWillUnmount`. Your `useEffect` can handle both of these by returning a function that will be invoked when cleanup is required.

```js
const [timer, setTimer] = useState(120);

useEffect(() => {
    const countdown = () => setTimer(t => t - 1);

    /* Creating an interval that calls the countdown function every 1 second (1000ms)*/
    const int = setInterval(countdown, 1000);

    /* Return the function you want trigger on clean up.
    In this case, I need to clear the interval */
    return () => clearInterval(int);
}, []); // Why do you think we want this dependency array here?

```

Read [the documentation](https://reactjs.org/docs/hooks-effect.html) for more details on the inner workings, things to look out for and more examples.

***

## Cardinal Rules
Hooks are just JS functions but with [two cardinal rules](https://reactjs.org/docs/hooks-rules.html)! Straight from the docs, they are:
1. `Only call Hooks at the top level. Don’t call Hooks inside loops, conditions, or nested functions`
2. `Only call Hooks from React function components. Don’t call Hooks from regular JavaScript functions. (There is just one other valid place to call Hooks — your own custom Hooks)`


Let's do a quick review of each of those rules:

### 1. "Only call Hooks at the top level"
The order that hooks are called in is important and needs to be consistent between renders. In the code below, see how the first example below (**NOT OK!**) only calls `useState` conditionally? This means unpredictable numbers of useState calls and will cause bugs!

**NOT OK!**
```js
import React, { useState, useEffect } from 'react';

const MyFunctionalComponent = ({ theSkyIsGreen }) => {
    let pigsCanFly, setPigsCanFly;
    
    useEffect(() => {
        if(theSkyIsGreen){
            [ pigsCanFly, setPigsCanFly ] = useState();
        }
    }, [theSkyIsGreen])

    return <h1>{pigsCanFly ? 'Flying Pigs!' : 'No flying here!'}</h1>
};
```


**OK!**
```js
import React, { useState, useEffect } from 'react';

const MyFunctionalComponent = ({ theSkyIsGreen }) => {
    const [ pigsCanFly, setPigsCanFly ] = useState();
    
    useEffect(() => {
        if(theSkyIsGreen){
            setPigsCanFly(true)
        }
    }, [theSkyIsGreen])

    return <h1>{pigsCanFly ? 'Flying Pigs!' : 'No flying here!'}</h1>
};
```
***
### 2. "Only call Hooks from React function components [ or ] Custom Hooks"
Outside of a React functional component, the only other place you can call Hooks from is a custom hook. ***Custom hook names start with `use`**.*

```js
// in customHooks.js
import { useState } from 'react';

export function usePigFlyStatus(theSkyIsGreen){
    const [ pigsCanFly, setPigsCanFly ] = useState();

    if(theSkyIsGreen){
        setPigsCanFly(true)
    }

    return pigsCanFly
}

// in MyFunctionalComponent.js
import React from 'react';
import { usePigFlyStatus } from '../customHooks';

const MyFunctionalComponent = ({ theSkyIsGreen }) => {
    const [ pigsCanFly, setPigsCanFly ] = usePigFlyStatus(theSkyIsGreen);

    return <h1>{pigsCanFly ? 'Flying Pigs!' : 'No flying here!'}</h1>
};

```