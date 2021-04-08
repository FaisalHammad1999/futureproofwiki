## State
React functional components can store local state data thanks to the `useState` hook. Only functional components can use hooks. \
_For state and eventing in class components please see the [class component version](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-State-and-Eventing-(Class-Components)) of this guide._

### `useState` Hook
**Initialising State** \
The [`useState` hook](https://reactjs.org/docs/hooks-state.html) returns an array with two elements. The first element is the value held in the current state. The second is a function we can use to update that value. Usually we use array destructuring to get access to both of these elements and store them in their own variables for use later in the component. Note that the destructuring is optional but tidies things up a lot!

You can store any data type you like in state. If you like, you can pass `useState` some data when you call it to set an initial state.

In the example below we are:
- initialising a piece of state with the string of `"futureproof"`
- creating two variables using array destructuring
- the first variable `username` gives us access to the state data (in this case 'futureproof')
- the second variable is a function with which we can update (more on this later!)

```jsx
import React, { useState } from 'react';

function MyComponent() {
    const[ username, setUsername ] = useState("futureproof");
}
```

**Multiple Pieces of State** \
We can use `useState` multiple times to get more than one piece of state:

```jsx
function App() {
   const [ alert, setAlert ] = useState("")
   const [ readCount, setReadCount ] = useState(0)
   const [ headlines, setHeadlines ] = useState([
      { headline: 'Disaster Strikes', snippet: 'It was a dark and stormy night...'},
      { headline: 'Sunny Days Ahead', snippet: 'Even in the UK, beach days are still upon us.'},
      { headline: 'Beware the Frumious Bandersnatch', snippet: 'Twas brillig, and the slithy toves did fyre and gimble in the wabe.'}
    ])
};

```

**_NB: You must only use useState (and other hooks) at the top level of your component - do not nest it within eg. an if block!_**

**Reading state** \
Reading from state is just like reading any other variable.
```jsx
import React, { useState } from 'react';

function MyComponent() {
    const[ username, setUsername ] = useState("futureproof");

    return (
        <h1>Hello, {username}!</h1>
    )
}
```

**Updating state** \
To make changes to state, **always use the setter function provided by `useState`**.

Direct manipulation (eg. `username = "Bob!"`) is to be avoided at all times as this will not trigger React's [reconciliation](https://reactjs.org/docs/reconciliation.html) process.

This process is extremely useful for us as it gets React to queue up the changes we are requesting, make sure that they are executed in the most efficient way and help ensure that the changes are reflected on the virtual DOM accurately.

```jsx
setUsername("Bob")
```


**If you need to reference state itself, pass a function** \
This callback function will receive a snapshot of the state as it was at the time the setter function was called. This is very important because the reconciliation process will queue up the state change with all the other jobs it has to do (including potentially other state changes) and we cannot guarantee that another process will not make an update to state before this one. Make sure that when you reference state in your returned object, to use that snapshot, not the original variable. You can name this argument whatever you like - I like to use `prevState` simply as an extra visual reminder!

```jsx
setReadCount(prevState => prevState + 1)
```

***

## Eventing
Often we are updating state as a reaction to an event. Perhaps a user clicks a 'Like' button and we increase the number of likes. Maybe they submit a form with their username and we then display their username in a welcome message.

Just as we have a 'virtual DOM' or, an abstraction of the DOM (which itself is already an abstraction!), React takes the browser event that we know and love and abstracts it into a [SyntheticEvent](https://reactjs.org/docs/events.html).

Many of the event names in React will look very familiar, just camel cased: `onClick`, `onHover`, `onSubmit` for example.

We can add them to any element, passing a reference to the handler function we want to be triggered on that event. Note that we **do not** invoke the function at this point. If you do need to pass an explicit argument to your handler, pass an arrow function eg: `onClick={() => doThis(someArgument)}`

```jsx
import React, { useState } from 'react';

function MyComponent() {
    const[ readCount, setReadCount ] = useState(0);

    const increaseReadCount = () => setReadCount(prevState => prevState + 1)

    return (
        <>
        <span>{readCount}</span>
        <button onClick={increaseReadCount} />
        </>
    )
}
```

Just as with our 'regular' event handlers, we still get access to the event itself:
```jsx
function MyForm(){
    const [ username, setUsername ] = useState("futureproof");

    const handleSubmit = e => {
      e.preventDefault();
      setUsername(e.target.nameInput.value);
    };

    return (
        <form onSubmit={handleFormSubmit}>
            <input type="text" name="nameInput" placeholder="That's not my name!"/>
            <input type="submit" value="Update!"/>
        </form>
    )
}
```

---

## Controlled Forms
Because in React we are usually keeping track of the state of our local data, it is best practice to use a [controlled forms](https://reactjs.org/docs/forms.html#controlled-components) pattern. Using our SyntheticEvent of `onChange` in conjunction with `setState` and an `input`'s `value` attribute, this is nice and easy! In this example, I've made an agnostic handler that can be used with any input.
```jsx
function MyForm(){
    const [ formData, setFormData ] = useState({ username: "", password: "" })
    
    const handleInput = e => {
        // using destructuring to access e.target.name and e.target.value
        const { name, value } = e.target;
        // note the use of the [square brackets] here so we can use the name variable (instead of accessing a key of .name)
        setFormData({ ...formData, [name]: value }); 
    };

    <form onSubmit={handleFormSubmit}>
        <input {/* You can multi-line if you like! */}
            type="text" name="nameInput" 
            placeholder="That's not my name!"
            value={formData.username} onChange={handleInput}
        />
        <input type="password" name="password" value={formData.password} onChange={handleInput} />
        <input type="submit" value="Update!"/>
    </form>
}
```