## Props
Passing data between components in a React app can be acheived with React Props (properties). \
Let's say we have some TV show data stored in state that we want to pass to a ShowCard component.
```jsx
state = { 
    faveShow: {title: 'The Good Place', seasons: 4}
}

// in render
<ShowCard show={this.state.faveShow}/>
```

From our `ShowCard` componont, we can access the props in one of two ways, depending on whether it is a class or a functional component:
```jsx
// In a Class Component, props can be accessed with `this.props`
class ShowCard extends Component {
    render(){
        return <p>{this.props.show.title} has {this.props.show.seasons} seasons.</p>
    };
};

// You can use destructuring
class ShowCard extends Component {
    render(){
        const { show: { title, seasons}} = this.props;

        return <p>{this.props.show.title} has {this.props.show.seasons} seasons.</p>
    };
};
```

```jsx
// In a functional component, props are accessed as a parameter
const ShowCard = (props) => {
    return <p>{props.show.title} has {props.show.seasons} seasons.</p>
};

// You can use destructuring
const ShowCard = ({show: { title, seasons }) => {
    return <p>{title} has {seasons} seasons.</p>
};
```

### Callbacks as props
As we know, functions can be treated in the same way as other data types in JavaScript. We can pass functions as props:
```jsx
class App extends Component {
    doSomething = e => console.log('Doing something!');

    render(){
        return <LikeButton clickHandler={this.doSomething} />
    };
};

const LikeButton = ({ clickHandler }) => <button onClick={clickHandler}>Click to do something!</button>
```

***

## Class Component Lifecycle Methods
React Class Components come with a suite of Lifecycle methods to give us some control over the lifecycle of a component instance. \
In one lifecycle, in the same way that humans are born once, develop several times through life and pass on once, React Components are 'mounted' once, can be 'updated' multiple times and 'unmounted' once. We don't know if humans can have more than one lifecycle but React Compononts certainly can!

***NB: function components can not access these methods***

[Here is a fantastic, simple diagram of all the lifecycle methods](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/). Note what is added/taken away when clicking on 'Show less common lifecycles'. As well as the 3 stages of mounting, updating and unmounting, we are also aware of a 2nd dimension of phases - render, pre-commit and commit. Reading the official React documentation on these methods is highly encouraged but here is a brief overview of the methods, starting with the most common ones.

### [constructor](https://reactjs.org/docs/react-component.html#constructor)
The constructor acts just like a regular class syntax constructor. Remember to call `super()` if you want to access `this` during the constructor. \
Usually used to define state, refs and bind functions. You may not often find that you need to use the constructor since we can now define state as a class property outside of a constructor function.
```jsx
constructor() {
    super()
    this.state = {
        msgCount: 0,
        darkMode: true
    }
    this.chatThreadRef = React.createRef()
}
```

### [render](https://reactjs.org/docs/react-component.html#render)
The only explicitly required lifecycle method. Without it, our component doesn't know what to put on the DOM!
```jsx
render(){
    return (<p>Render me!</p>)
}
```

### [componentDidMount](https://reactjs.org/docs/react-component.html#componentdidmount)
This is the first point at which you can trigger side effects. A great place to make initial API calls and start intervals or timers. \
Only runs once, after component has finished mounting.
```jsx
componentDidMount(){ this.commenceTheStream(); }

commenceTheStream = () => {
    this.fetchAMeme();
    const interval = setInterval(this.fetchAMeme, 2000);
    this.setState({ interval })
}
```

### [componentDidUpdate](https://reactjs.org/docs/react-component.html#componentdidupdate)
Like cDM but happens after each re-render. You can trigger side-effects and use `setState` here but be mindful that this method will run after every update (unless intercepted by `shouldComponentUpdate`) so you may end up in a never ending cycle of re-renders if you are not careful! If `getSnapshotBeforeUpdate` returned data, it is available here as the third parameter.
Receives `prevProps`, `prevState` and optional `snapshot`, can access `this.props` and `this.state`.
```jsx
componentDidUpdate(prevProps, prevState, snapshot) {
    if (snapshot) {
        const chatThreadRef = this.chatThreadRef.current;
        chatThreadRef.scrollTop = chatThreadRef.scrollHeight - snapshot;
    }
}
```

### [componentWillUnmount](https://reactjs.org/docs/react-component.html#componentwillunmount)
This is for last-minute tidying up before unmounting. Clear any intervals or timers you had setup here. Only runs once, just before component unmounts.
```jsx
componentWillUnmount(){ this.stopTheMemes(); }

stopTheMemes = () => clearInterval(this.state.interval);
```

### [getDerivedStateFromProps](https://reactjs.org/docs/react-component.html#static-getderivedstatefromprops)
Not commonly used - there are usually better ways to do whatever it was you were thinking of doing here! `static` method. \
Receives `props` and `state`, should return a new object that will be the new state, or return `null` for no changes to be made.
```jsx
static getDerivedStateFromProps(props, state){
    if(props.messages.length > state.msgCount &&
        props.messages[props.messages.length-1].body === 'switch theme' ){
            return { darkMode: !state.darkMode, msgCount: props.messages.length }
    }
    return null;
}
```

### [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)
This runs by default with a return value of `true`. To intercept and make a calculated decision on whether the component should proceed with the update, we can explicitly define `shouldComponentUpdate`. Receives `nextProps` and `nextState` , can access `this.props` and `this.state`. Should return boolean value.
```jsx
shouldComponentUpdate(nextProps, nextState){
    return (this.props.messages.length !== nextProps.messages.length);
}
```

### [getSnapshotBeforeUpdate](https://reactjs.org/docs/react-component.html#getsnapshotbeforeupdate)
Another fairly uncommon one. It allows us to take a 'snapshot' of any data so we can pass it as `componontDidUpdate`'s 3rd parameter, where we can react to the data received. \
Receives `prevProps` and `prevState` , can access `this.props` and `this.state`.
```jsx
getSnapshotBeforeUpdate(prevProps, prevState) {
    if (this.props.messages.length > prevProps.messages.length) {
        const chatThreadRef = this.chatThreadRef.current;
        return chatThreadRef.scrollHeight - chatThreadRef.scrollTop;
    }
    return null;
}
```

***

## Navigation with [React Router](https://reactrouter.com/web/guides/quick-start)
As React is used to create SPAs (Single Page Applications) we can be tempted to ignore actual browser navigation but this is not very fair on users who may want to bookmark a view on your app or use their browser navigation system (foward, back, history etc).

`react-router-dom` is an extremely popular library to handle navigation in React. Here are some of the key features to get you up and running.
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

### Defining Routing
What we are going to add is essentially a fancy `switch` statement to decide which view to show based on the url path. We'll use the `Switch` and `Route` components from `react-router-dom` for this. The `Route` can either receive a `render` prop or a `component` prop to define what should be shown when that route is visited. Note the differences below.
```jsx
// anywhere you need routing
import { Switch, Route } from 'react-router-dom';

// in render/return
<Switch>
    {/* pass render a function that returns some jsx */}
    <Route exact path="/" render={() => <h1 id="welcome">Welcome</h1>} />
    {/* pass a component as a prop */}
    <Route path="/instructors" component={InstructorsContainer} />
    {/* pass render a function that receives props as an argument and injects them into the returned component */}
    <Route path="/students" render={props => <StudentsContainer {...props} students={this.state.allStudents}/>} />
    {/* if the url path doesn't match any this will run */}
    <Route component={NotFound404} />
</Switch>
```

### Dynamic Segments
Note that the `Switch` is checking the Route paths top to bottom to find a match. This is especially important when handling nested routing with dynamic segments.
```jsx
<Switch>
    {/* If we switched these two Routes around, /new would get caught as an :id segment */}
    <Route path={`students/new`} render={() => <StudentForm handleSubmit={this.addStudent}/>}/>
    <Route path={`students/:id`} render={() => <PersonCard getPerson={this.getStudentByName}/>}/>
</Switch>
```

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

export default withRouter(App); // here is where we pass our App component to withRouter. Check out the dev tools to see the result!
```
```jsx
// rendered on a page with path of `/cats/:name`
const CatCard = ({allCats, match}) => {
    // Extracting specific route parameter with match prop
    const cat = allCats.find(cat => cat.name === match.params.name)

    const renderUnknown = <p>I'm sorry we don't have a cat called {match.params.id} here!</p>

    const renderCat = () => <p>{cat.name} is {cat.age} years old</p>

    return ({ cat ? renderCat() : renderUnknown })
}
```