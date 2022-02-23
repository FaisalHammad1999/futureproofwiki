As React is used to create SPAs (Single Page Applications) we can be tempted to ignore actual browser navigation but this is not very fair on users who may want to bookmark a view on your app or use their browser navigation system (foward, back, history etc).

[`react-router-dom`](https://reactrouter.com/web/guides/quick-start) is an extremely popular library to handle navigation in React. Here are some of the key features to get you up and running.

_Note that React Router had a major update in 2021 to v6. You might well still see v5 in use out there but fear not - you can always check out our original [React Router v5](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-State-and-Eventing-(Class-Components)) version of this guide._

***

- [Setup](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#setup)
- [Routes](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#defining-routes)
- [Links](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#creating-links)
- [Setup](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#setup)
- [Hooks](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Navigation#hooks)

---
### Setup
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
### Defining Routing
What we are going to add is essentially a fancy `switch` statement to decide which view to show based on the url path. We'll use the `Switch` and `Route` components from `react-router-dom` for this. The `Route` can wrap around child elements (***NB: this is the preferred option when using hooks***) or can receive a `render` prop or a `component` prop to define what should be shown when that route is visited. Note the differences below. The render or component props approach is more likely to be found in routes that render Class Components.
```jsx
// anywhere you need routing
import { Switch, Route } from 'react-router-dom';

// in render/return
<Switch>
    {/* pass render a function that returns some jsx */}
    <Route exact path="/" render={() => <h1 id="welcome">Welcome</h1>} />

    {/* wrap the Route around a component PREFERRED APPROACH WHEN USING HOOKS */}
    <Route path="/schools"><Schools select={selectSchool} /></Route>

    {/* pass a component as a prop */}
    <Route path="/instructors" component={InstructorsContainer} />

    {/* pass render a function that receives props as an argument and injects them into the returned component */}
    <Route path="/students" render={props => <StudentsContainer {...props} students={this.state.allStudents}/>} />

    {/* if the url path doesn't match any this will run */}
    <Route><NotFound404 /></Route>
</Switch>
```
***
### Dynamic Segments
Note that the `Switch` is checking the Route paths top to bottom to find a match. This is especially important when handling nested routing with dynamic segments.
```jsx
<Switch>
    {/* If we switched these two Routes around, /new would get caught as an :id segment */}
    <Route path={`/students/new`}><StudentForm handleSubmit={this.addStudent}/></Route>
    <Route path={`/students/:id`}><PersonCard getPerson={this.getStudentByName}/></Route>
</Switch>
```

You can also have optional dynamic segments by adding the `?` after the optional segment. The below Route will render its children if the path is "/profile" and also if it has a dynamic segment eg. "/profile/futureproof".
```jsx
<Route path="/profile/:username?"><Pages.Profile /></Route>
```
***
### Creating Links
We can use normal `<a>` tags to create links but using `react-router-doms`'s `Link` and `NavLink` components give us more integrated control over our navigation handling.
```jsx
import { Link } from `react-router-dom`;

// in render/return
<Link to="/contact">Visit our contact page</Link>
```
`NavLink` gives an nice bonus of an `activeClassName` prop. When we are on that page, the class will added for us.
```jsx
import { NavLink } from `react-router-dom`;

// in render/return
<NavLink to="/treasure" activeClassName="current">Here Be Treasure</NavLink>
<NavLink to="/cats" activeClassName="current">Cats</NavLink>
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

---

## Accessing Routing Data
To give your components access to data about the routing, you can either use the [hooks provided by React Router](https://reactrouter.com/web/api/Hooks) **or** you can use their Higher Order Component, [`withRouter`](https://reactrouter.com/web/api/withRouter).

***When using functional components, we recommend using the hooks where possible.***

### Hooks
#### `useHistory`
`useHistory` gives us access to the [history instance](https://reactrouter.com/web/api/history)

Two common uses are the `goBack` function:
```jsx
import { useHistory } from 'react-router-dom';

const BackButton = () => {
    const history = useHistory();

    return <button id="back-button" onClick={history.goBack}>Back</button>
}
```

and programmatic navigation:
```js
    const history = useHistory();

    addStudent = e => {
        e.preventDefault();
        const newStudent = { id: students.length + 1, name: nameInput }
        setStudents(prev => [...prev, newStudent])
        // Redirecting to the new student's show page after submission
        history.push(`/students/${newStudent.id}`)
    }
```

---

#### `useLocation`
`useLocation` gives us access to the [location object](https://reactrouter.com/web/api/location)

---

#### `useParams`
`useParams` gives us access to the [params object](https://reactrouter.com/web/api/Hooks/useparams)

---

#### `useRouteMatch`
`useRouteMatch` gives us programatic access to the same [match](https://reactrouter.com/web/api/Hooks/useroutematch) logic that is used when matching for `<Route>`s

---

### Testing
When testing components that need basic access to a React Router, we can use MemoryRouter:
```js
import { MemoryRouter } from 'react-router-dom';
    // ...
    render(<News />, { wrapper: MemoryRouter })
    // ...
```
For more control over testing components that use React Router, check out the [documentation](https://testing-library.com/docs/example-react-router/).


---

### withRouter
`withRouter` is a [Higher Order Component](https://reactjs.org/docs/higher-order-components.html) that comes with `react-router-dom`. We can use it to wrap a Component and give it access to our router props of `match`, `history` and `location`. There is quite a bit to explore in those but here are some of the most common uses:

```jsx
import { withRouter } from `react-router-dom`;

class CatsContainer extends Component  {
    state = { allCats: [ { name: 'Zelda', age: 3 }, { name: 'Ziggy', age: 2 } ] };

    addCat = e => {
        e.preventDefault();
        const { name, age } = e.target;
        const newCat = { name: name.value, age: age.value };
        conset allCats = [ ...this.state.allCats, newCat ];
        this.setState({ allCats });
        // Redirecting to the new cat's show page after submission using history prop
        this.props.history.push(`/cats/${newCat.name}`)
    }

    render(){
        <>
            <button>
                {   {/* Checking current full path with location prop */}
                    this.props.location.pathname !== '/cats/new' ?
                        {/* Accessing the base path with match prop */}
                        <Link to={`${this.props.match.url}/new`}>Add a Student</Link>
                        {/* Programatically going back a page in browser history with history prop */}
                        : <span onClick={this.props.history.goBack}>Back</span>
                }
            </button>
        </>
    };
}

export default withRouter(CatsContainer); // here is where we pass our CatsContainer component to withRouter. Check out the dev tools to see the result!
```
```jsx
// rendered on a page with path of `/cats/:name`
import { withRouter } from `react-router-dom`;

const CatCard = ({allCats, match}) => {
    // Extracting specific route parameter with match prop
    const cat = allCats.find(cat => cat.name === match.params.name)

    const renderUnknown = <p>I'm sorry we don't have a cat called {match.params.id} here!</p>

    const renderCat = () => <p>{cat.name} is {cat.age} years old</p>

    return ({ cat ? renderCat() : renderUnknown })
}

export default withRouter(CatCard);

```

---