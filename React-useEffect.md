React functional components can trigger effects with the `useEffect` hook. Only functional components can use hooks. \
_For handling this in class components, please see our [React class component lifecycle methods](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Component-Lifecycle-Methods) guide._

## Why `useEffect`?
We are frequently causing **side effects** when writing blocks of code. A side effect is commonly defined as _"[...] anything that affects something outside the scope of the function being executed"_. This might be a fetch request that results in updating what our user sees, it might be setting an interval to generate a random number every 10 seconds, it could also be direct manipulation of the DOM _(which in general we try to avoid when using best practice)_

`useEffect` enables us to eloquently handle side effects without putting a spanner in the works when in comes to the render and re-render logic that React provides us with. It needs just two pieces of information: what to do (a callback function) and when to do it (an array of 'dependencies' that it will listen out for changes on)

---

## Where to `useEffect`?
As long as we follow the cardinal rules of hooks, we can useEffect in any functional component, and we can use as many as we desire within one component!

Hooks are just JS functions but with [two cardinal rules](https://reactjs.org/docs/hooks-rules.html)! Straight from the docs, they are:
1. Only call Hooks at the top level. Don’t call Hooks inside loops, conditions, or nested functions
2. Only call Hooks from React function components. Don’t call Hooks from regular JavaScript functions. (There is just one other valid place to call Hooks — your own custom Hooks)

---

## How to `useEffect`?
`useEffect` hooks take two arguments - a function (the 'effect') and an optional dependency array. It is important to consider what you need (if anything) in your dependency array.

By default, your effect will run every time a render or re-render is triggered but usually we will only want it to run if something changes.
```jsx
import React, { useEffect } from 'react';

const MyFunc = () => {
    useEffect(() => {
        console.log('I run on every single render! I'm very good at causing infinite render loops!')
    })
}
```

By passing an empty dependency array, the effect will run once only and never again as it has no dependencies. 
```jsx
useEffect(() => { console.log('mounted') }, [])
```

This is not actually as common as we may think and usually we are relying on a dependency, even if that happens to only 'change' once in the lifecycle. Consider this example:
```jsx
const CatCard = ({ catId }) => {
    useEffect(() => {
        async function getCat(){
            let data = await axios.get(`https://myapi.com/cats/${catId}`)
            setCatName(data.name)
        }
        getCat()
    }, [ catId ])
}
```

By passing a populated array, the effect will run only if that attribute has changed since the last render.

**_Check out [this demo](https://codesandbox.io/s/useeffect-dependencies-4pu91?file=/src/App.js) for a working example_**

---

## Clean up
Another popular side effect is to set an interval or timer of some kind. This behaviour requires 'cleanup'. In a class component you might set an interval in `componentDidMount` and clear it in `componentWillUnmount`. Your `useEffect` can handle both of these by returning a function that will be invoked when cleanup is required.

Consider this example that runs a countdown timer that starts at 120 and decreases by 1 every second. The return function makes sure the interval is cleared when the component is unmounted. That way we will avoid the dreaded error that we cannot update an unmounted component.
```js
import React, { useState, useEffect } from 'react';

const MyTimer = () => {
    const [ timer, setTimer ] = useState(120)

    useEffect(() => {
        const countdown = () => setTimer(t => t - 1);

        /* Creating an interval that calls the countdown function every 1 second (1000ms)*/
        const int = setInterval(countdown, 1000);

        /* Return the function you want trigger on clean up.
        In this case, I need to clear the interval */
        return () => clearInterval(int);
    }, []);
}

---

## Multiple `useEffects`
As long as we stick to the cardinal rules of hooks, we can use as many `useEffect`s in a component as we desire

```jsx
import React, { useState, useEffect } from 'react';

const MyTimer = ({ startingTime }) => {
    const [ timer, setTimer ] = useState()

    useEffect(() => {
        setTimer(startingTime)
        const countdown = () => setTimer(t => t - 1);

        /* Creating an interval that calls the countdown function every 1 second (1000ms)*/
        const int = setInterval(countdown, 1000);

        /* Return the function you want trigger on clean up.
        In this case, I need to clear the interval */
        return () => clearInterval(int);
    }, [startingTime]);
}
```

_*Check out [this example](https://codesandbox.io/s/useeffect-countdown-w7ryj) for a slightly fancier countdown timer powered by `useEffect`!*_

---

***Read the [useEffect documentation](https://reactjs.org/docs/hooks-effect.html) for more details on the inner workings, things to look out for and more examples.***