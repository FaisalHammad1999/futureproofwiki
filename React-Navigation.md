As React is used to create SPAs (Single Page Applications) we can be tempted to ignore actual browser navigation but this is not very fair on users who may want to bookmark a view on your app or use their browser navigation system (foward, back, history etc).

[`react-router-dom`](https://reactrouter.com/docs/en/v6/) is an extremely popular library to handle navigation in React. Here are some of the key features to get you up and running.

> _Note that React Router had a major update in 2021 to v6 which is what this page will cover. v6 does require React 16.8 or above and you might well still see v5 in use out there but fear not - you can always check out our original [React Router v5](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-State-and-Eventing-(Class-Components)) version of this guide._

***

- [Install & Setup](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#installation)
- [Routing](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#defining-routing)
- [Links](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#creating-links)
- [Navigation & More](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#hooks)
- [Testing](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#testing)
---
## Installation
To ensure you are working with v6, install to your project with: \
`npm i react-router-dom@6`

---
## Setup
As high up as possible in your app, wrap your app in a Router. Generally I like to do this in my `index.js` file.
```jsx
// in index.js
import { BrowserRouter } from 'react-router-dom';

ReactDOM.render(
    <BrowserRouter>
      <App />
    </BrowserRouter>
  document.getElementById('root')
);
```
***
## Routing

### Routing basics
What we are going to add is essentially a very intelligent `switch` statement to decide which view to show based on the url path. We'll use the `Routes` and `Route` components from `react-router-dom` for this.

`Routes` acts as a container for all our `Route` elements.

`Route` elements commonly take props of `path` and `element`.


```jsx
// wherever you are defining your routing
import { Routes, Route } from 'react-router-dom';

// in return
<Routes>
    <Route path="/" element={<Landing />} />
    <Route path="cohorts" element={<Cohorts />} />
    <Route path="news" element={<News />} />
    <Route path="about" element={<About />} />
    <Route path="*" element={<NotFound />} />
</Routes>
```

### Nested routes and the `index` route
> _If you are coming from React Router v5, prepare to be excited because this looks a lot neater and leaner in v6!_

`Route` components can be placed within other `Route` components for nested routing.
In a `Route` that has multiple children, we can assign one of them the `index` attribute to indicate what to render when the URL is not extended. Check out the example below and see if you can spot this happening twice.


```jsx
<Routes>
    // EVERY path will render the Layout
    <Route path="/" element={<Layout />}>
        // the `/` path will render the `Landing` component
        <Route index element={<Landing />} />

        // EVERY path that starts `/cohorts` will render the `CohortsContainer`
        <Route path="cohorts" element={<CohortsContainer />}>
            // the `/cohorts` path will render the `CohortsIndex` component
            <Route index element={<CohortsIndex />}/>
            // the `/cohorts/:cohortName` path will render the `Cohort` component
            <Route path=":cohortName" element={<Cohort />} />
            // the `/cohorts/new` path will show the `NewCohortForm` component
            <Route path="new" element={<NewCohortForm />}/>
        </Route>

        <Route path="news" element={<News />} />
        <Route path="about" element={<About />} />
        <Route path="*" element={<NotFound />} />
    </Route>
</Routes>
```

> :bulb: _If you're wondering how `/cohorts/new` can go after the dynamic `/cohorts/:cohortName` route, good question! The v6 `Routes` logic is more advanced than in the v5 `Switch` component and will always look for the closest match so we can be more relaxed about the order._

#### Rendering nested routes
Now the logic of **what** to render is neatly placed together, how will we state **where** to render the nested elements?

The key is in the [`Outlet`](https://reactrouter.com/docs/en/v6/api#outlet) component provided in React Router v6. Based on the routing example given above, we might expect to see something like this in our `Layout` component:

```jsx
import { Outlet } from "react-router-dom";
import { Header, Footer } from "./my-custom-components"

function Layout() {
  return (
    <Header />
    <main>
        // depending on the path, this Outlet will render either the Landing, CohortsContainer, News, About or NotFound component
        <Outlet />
    </main>
    <Footer />
  );
}
```

And perhaps our `CohortsContainer` would look something like this:
```jsx
import { Outlet } from "react-router-dom";

function CohortsContainer() {
  return (
    <h1>futureproof Cohorts</h1>
    // depending on the path, this Outlet will render either the CohortsIndex, Cohort or NewCohortForm component
    <Outlet />
  );
}
```


#### Dynamic Segments
In our routing example above we have a `<Route path=":cohortName" element={<Cohort />} />`.

The `:` syntax states that this is a dynamic segment / URL parameter and implies that this `Cohort` element is likely to render different content depending on the `:cohortName`. To access this segment so we can decide what to do with it, we can harness the given `useParams` hook.

`useParams` returns an object where the keys are any defined dynamic segment names and the value is... the value!

In our example here, if our user navigates to `/cohorts/lytical`, `useParams()` would return an object of `{ cohortName: "lytical" }`.

```jsx
import { useParams } from "react-router-dom";

function Cohort() {
    let params = useParams();

    return (
        <h1>Meet the ${params.cohortName} cohort!</h1>
    )
}
```

A very common use case might be to read the param, request some data based on that and update the page accordingly. Read through the following example carefully and follow the logic.
```jsx
import { useParams } from "react-router-dom";
import { useState, useEffect } from "react";

import { Headshot, Banner } from "../my-custom-components";

function Cohort() {
    let { cohortName } = useParams(); // optional use of object destructuring
    let [loading, setLoading] = useState();
    let [error, setError] = useState();
    let [studentList, setStudentList] = useState();

    useEffect(() => {
        const fetchCohortData = async () => {
            try {
                setLoading(true);
                let { students } = await fetch(`https://ourapi.com/cohorts/${cohortName}`);
                if(!students){ throw new Error("Cohort not found")};
                setCohortData(data);
                setLoading(false);
            } catch (e) {
                setLoading(false);
                setError(e.message);
            }
        }
    }, [cohortName])

    const renderStudents = () => studentList.map(st => <Headshot key={st.id} student={st}/>)

    const renderError = () => <span class="error"> Oops! {error} </span>

    return (
        { error ? <Banner type="error" msg={error} />: null }

        <section id="students">
            { loading ? <h1>Loading cohort...</h1> : renderStudents() }
        </section>
    )
}
```


***
## Navigation

### User navigation
Whilst you technically can use normal `<a>` tags to create your internal links, using `react-router-dom`'s `Link` and `NavLink` components give us much more integrated control over our navigation handling.
```jsx
import { Link } from `react-router-dom`;

// in return
<Link to="/contact">Visit our contact page</Link>
```


`NavLink` is very similar to a `Link` but has awareness of its "active" state - ideal for styling and accessibilty on nav bars and the like.
By default, an active NavLink will receive a class of `active`.

```jsx
import { NavLink } from `react-router-dom`;

// in return
<nav>
    <NavLink to="/treasure">Here Be Treasure</NavLink>
    <NavLink to="/cats">Cats</NavLink>
</nav>
```

```css
nav a {
    font-weight: normal;
}

nav a.active {
    font-weight: bold;
}
```

You can customise this further by using the boolean `isActive` value which is received by a function that can be assigned to a `NavLink`'s `style` or `className` prop:

```jsx
<nav>
    <NavLink
        to="/treasure"
        className={({ isActive }) => isActive ? "discovered" : "undiscovered" } >
            Here Be Treasure
    </NavLink>

    <NavLink
        to="/cats"
        style={({ isActive }) => isActive ? ({ textDecoration: "underline" }) : ({ textDecoration: "none" }) }>
        Cats
    </NavLink>
</nav>
```

```css
    nav a.undiscovered {
        color: grey;
    }

    nav a.discovered {
        color: gold;
    }
```


**NB**: *At time of writing, a conflicting dependency version of the `path-to-regexp` module can cause the path to not be matched. If you are having trouble with this, in your `webpack.config.js` file, add the following `alias` key to `config.resolve` and restart your dev server:*
```js
const ROOT_DIRECTORY = path.join(__dirname, '../');

const config = {
    ...,
    resolve: {
        alias: {
            'path-to-regexp': path.resolve(ROOT_DIRECTORY, 'node_modules', 'react-router', 'node_modules', 'path-to-regexp')
        },
        ...
    },
    ...
}
```

### Programmatic navigation
Sometimes we want to navigate programatically. The provided `useNavigate` hook return a function that allows exactly this.


We can specify a number of steps to take based on the history being racked up by our Router:
```jsx
import { useNavigate } from "react-router-dom";

function BackButton() {
  let navigate = useNavigate(); // navigate is a more common name for the returned function than goTo!

    return <button onClick={() => navigate(-1)}>Back</button>
}
```

Or can request a specific path:
```jsx
import { useNavigate } from "react-router-dom";

function ContactForm() {
  let goTo = useNavigate(); // Name it anything you like, I'm a fan of "goTo" as that's what it lets you do!

  async function handleSubmit(e) {
    e.preventDefault();
    await submitForm(e.target);
    goTo("../thankyou");
  }

  return <form onSubmit={handleSubmit}>{/* ... */}</form>;
}
```

An optional second argument of an options object can be passed with keys of `replace` to overwrite the last history entry and/or `state` to pass some data along with the navigation.

To access any passed `state` at the next location, use the `useLocation` hook. In the example below, when our contact form is submitted, it navigates to `/thankyou` and sends along the first name value from the form.

```jsx
import { useNavigate } from "react-router-dom";

function ContactForm() {
    let goTo = useNavigate(); 

    async function handleSubmit(e) {
        e.preventDefault();
        await submitForm(e.target);
        goTo("/thankyou", { replace: true, state: {name: e.target.firstName.value} });
    }

    return (
        <form onSubmit={handleSubmit}>
        // this form should really be controlled!
            <input type="text" id="firstName" />
            <input type="text" id="lastName" />
            <textarea id="message" rows="4" cols="50" />
            <input type="submit" />
        </form>
    );
}

import { useLocation } from "react-router-dom";

function Thankyou() {
    let location = useLocation(); 

    return (
        <h1>Thanks for getting in touch, {location.state.firstName}</h1>
    );
}
```

For more info check out the [`useNavigate`](https://reactrouter.com/docs/en/v6/api#usenavigate) and [`useLocation`](https://reactrouter.com/docs/en/v6/api#uselocation) docs.


---

### More...
THere are plenty more awesome things to discover about React Router v6, check out the [documentation](https://reactrouter.com/docs/en/v6/api)!

---

### Testing
When testing components that need basic access to a React Router, we can use MemoryRouter:
```js
import { MemoryRouter } from 'react-router-dom';
    // ...
    render(<News />, { wrapper: MemoryRouter })
    // ...
```
For more control over testing components that use React Router, including creating a fake history to navigate through, check out the [documentation](https://testing-library.com/docs/example-react-router/).

---