So far we have got quite familiar with `useState`, `useEffect` and some of the React Router hooks. There are many more at our disposal from various different sources - the core React library, 3rd party libraries and hooks we write ourselves.

Here we will cover just two additional in built hooks which you may well find useful in your upcoming work, and we will also cover how to write your own hooks. At the end of this guide, there is an example of how we can combine these together, creating a custom hook to encapsulate a more complex implementation of `useContext`.

**_Check out the [official documentation](https://reactjs.org/docs/hooks-reference.html) for the hooks that come with the core React library. \
As you can see there are more than we cover here and as they say themselves, "Don’t stress about learning them up front."_**

---

## useRef
When working in React, we like to avoid direct query or manipulation of the DOM so as to make the most of the React flow. So what if we want to do something to a similar effect of a `document.querySelector('#select-me')`? This is where `useRef` comes into play. A very popular usage of this is to update scroll position. When starting out with `useRef`, make sure to remember that it returns an object, not the element itself. In the example below, note the the element is stored in the `"current"` key of the `useRef` object.

The most common usage of `useRef` is for referencing DOM elements, but it can hold any data you like!

```jsx
import React, { useState, useEffect, useRef } from 'react';

const NotePad = () => {
    const [ notes, setNotes ] = useState([{ timestamp: Date.now(), body: 'Welcome to your NotePad!'}])
    const [ input, setInput ] = useState("")
    const notesEnd = useRef();

    useEffect(() => {
        notesEnd.current.scrollIntoView({ behavior: "smooth" })
    }, [notes])

    const handleChange = e => setInput(e.target.value)

    const handleSubmit = e => {
        e.preventDefault();
        let newNote = { timestamp: Date.now(), body: input }
        setInput('')
        setNotes(prev => [ ...prev, newNote ])
    }

    const renderNotes = () => notes.map((n, i) => <p key={i}>{n.body}</p>)

    return (
        <section id="notepad">
            <div id="notes-container">
                { renderNotes() }
                <span ref={notesEnd}></span>
            </div>
            <form onSubmit={handleSubmit}>
                <input type="text" value={input} onChange={handleChange}/>
            </form>
        </section>
    )
}
```

---

## useContext
`useContext` provides us with a way to access a piece of data from any of our components without relying on trickle-down props. If you're thinking that this sounds a bit like redux then you're on the right track. `useContext` is a much more limited solution to the same issue but can be extremely effective when use appropriately.

Once a context has been **Created**, there are two stages of its implementation: **Providing** the data and **Accessing** the data.

**Creating context**
```js
const LanguageContext = React.createContext()
```

**Providing**
```jsx
<LanguageContext value={"en"} />
    <App />
</LanguageContext>
```

**Accessing**
```jsx
import React, { useContext } from 'react';
import { LanguageContext } from '/path/to/context';

const AComponentToTranslate = () => {
    const language = useContext(LanguageContext);

    return <section>Translating content to {language}</section>
}
```


---

##  customHooks
As we know, hooks are just functions so there is no reason we cannot create our own. Aside from within React functional components, they are the only other place we can utilise other hooks. Custom hooks are perfect for abstracting out complex and/or reused functionality.

_**Custom hooks must follow the hooks naming convention of starting with `use`**_

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
```

```js
// in MyFunctionalComponent.js
import React from 'react';
import { usePigFlyStatus } from '../customHooks';

const MyFunctionalComponent = ({ theSkyIsGreen }) => {
    const pigsCanFly = usePigFlyStatus(theSkyIsGreen);

    return <h1>{pigsCanFly ? 'Flying Pigs!' : 'Nothing to see here...'}</h1>
};
```

---

##  Useful Combo Implementation
Consider this implementation of a theme context. Note how it is a little cumbersome, so we have abstracted it to a custom hook.

**Creating context and custom hook**
```js
// /src/contexts/theme.js
import React, { useState, useContext } from 'react';

const ThemeContext = React.createContext();

export const useThemeContext = () => useContext(ThemeContext);

export function ThemeProvider({ children }){
    const [ darkMode, setDarkMode ] = useState(true);

    const toggleTheme = () => setDarkMode(prev => !prev);

    return (
        <ThemeContext.Provider value={{ darkMode, toggleTheme }} >
            { children }
        </ThemeContext.Provider>
    )
}
```

**Wrapping app in custom Provider**
```js
// /src/index.js
import { ThemeProvider } from "./contexts/theme";
import App from './App';

ReactDOM.render(
    <ThemeProvider
        <App />
    </ThemeProvider>,
    document.getElementById('root')
);
```

**Accessing functionality via custom hook**
```jsx
// /src/layout/Header.js
import { useThemeContext } from './contexts/theme';

function Header(){
    const { darkMode, toggleTheme } = useTheme();

    return (
        <div>
            <h1>Welcome</h1>
            <p>Current theme: {darkMode ? "Dark" : "Light"}</p>
            <button onClick={toggleTheme}>Toggle Theme</button>
        </div>
    )
}
```

_**futureproof students can see a full authentication solution using `useContext` and custom hooks in [the demo repo](https://github.com/getfutureproof/fp_study_notes_advanced_hooks)**_
