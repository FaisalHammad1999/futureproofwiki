Redux is a **global state management pattern** that can be used in many situations but is commonly associated with React applications. \
Whilst redux is a pattern, there is also a very popular library called [`redux`](https://redux.js.org/introduction/getting-started) that provides us with some helpful tools to implement this pattern along with [`react-redux`](http://react-redux.js.org/) \
When you hear someone refer to 'redux' they very often are referring to the combination of these libraries but it is worth remembering that it is also the name of the pattern that the libraries help us to implement.

### The Problem
We have seen how we can trickle data down to components via props and also pass callbacks as props to partially help us get data back up. Because props can only move up and down, not sideways, this can often mean that our props end up being passed through components that don't actually need them, just to get down to the correct layer.

### The Solution
A redux ***store*** allows us to keep our data separately to our components. When we ***connect*** a component to the store, we can directly access the data that is required. \
When we need to write or update data to our store, we can send an ***action object*** to a ***reducer function*** which acts based on the information it has received and returns a new ***state object*** to represent our data in our store.

## Installation & Setup
### Install
Install with `npm install redux react-redux redux-devtools-extension` \
We only really 'need' redux and react-redux but the devtools extension is extremely useful for debugging so grab that as well!
***
### Create a reducer
A reducer is a function that takes in state data and an action object. It returns new state data. \
```js
// in reducers/catsReducer.js
const initState = { all: [{ id: 101, name: "Zelda", age: 3 }], favourite: null }

// On initial creation, redux will call our reducer with an action object of { type: "@@INIT" }
// It will not yet have a state to pass in so we define an initState and pass in as a default argument for this first call.
const catsReducer = (state=initState, action) => {
    // Inside the reducer function, a switch statement will examine the action object's `type` value and decide what to do
    switch(action.type){
        case: "ADD_A_CAT": // Expecting an action like { type: "ADD_A_CAT", payload: { id: 235, name: Tigerlily, age: 10 } }
            const newCat = action.payload;
            // Remember to use the spread operator to make sure you don't lose the rest of your state object
            // This is NOT like React's setState!
            return { ...state, all: [ ...state.all, newCat ] };
        case: "SELECT_FAVE_CAT": // Expecting an action like { type: "SELECT_FAVE_CAT", payload: 235 }
            const faveCat = state.all.find(c => c.id ==== action.payload);
            return { ...state, favourite: faveCat }
        case: "UPDATE_A_CAT": // Expecting an action like { type: "UPDATE_A_CAT", payload: { id: 101, name: "Princess Zelda", age: 3 } }
            const catToUpdate = state.all.find(c => c.id === action.payload.id);
            const index = state.all.indexOf(catToUpdate);
            // Using slice is very common for this logic, it's worth getting comfy with slice!
            const updatedCats = [ ...state.all.slice(0, index), action.payload, ...state.all.slice(index + 1)]
            return { ...state, all: updatedCats}
        // The default case is essential for the "@@INIT" action to run
        default:
            return state;
    }
}

// Remember to export it so we can access from other files!
export default catsReducer;
```
***

### Make some action creators
An ***action*** is an object which has a "type" key. An ***action creator*** is a function which returns an action object. \
You may hear these used interchangeably but bear in mind the difference! Confusingly, action creators are often kept in a folder called `actions` which doesn't help! \
The returned actions will be `dispatch`ed to our reducer - see how the action types reflect the switch logic in the reducer above.

```js
// in actions/catActions.js
export default addCat = e => ({ // imagine this being triggered in a form submit flow, receiving the form event
    type: "ADD_A_CAT",
    payload: { name: e.target.name, age: e.target.age }
})

export default changeFavouriteCat = id => ({ // imagine this being passed a cat's id
    type: "SELECT_FAVE_CAT",
    payload: id
})
```

***

### Create a store
**If you only have one reducer**:
```js
// in index.js
import { createStore } from 'redux';
import catsReducer from './reducers/catsReducer';
import { devToolsEnhancer } from 'redux-devtools-extension';

const store = createStore(catsReducer, devToolsEnhancer());
```

**If you are using more than one reducer**, use the `combineReducers` method to bring them together into one store. \
In this case I've moved this logic to another file `store.js` to keep things tidy. You could still do all this in index.js. Likewise, you could do the above with one reducer in a separate file if you like.
```js
// in store.js
import { createStore, combineReducers } from 'redux';
import studentsReducer from './reducers/studentsReducer';
import instructorsReducer from './reducers/instructorsReducer';
import { devToolsEnhancer } from 'redux-devtools-extension';

export const store = createStore(combineReducers({ 
    students: studentsReducer, 
    instructors: instructorsReducer 
}), devToolsEnhancer());
```

**If you are using a piece of middleware** eg [`redux-thunk`](https://www.npmjs.com/package/redux-thunk) you need to add it when creating the store. \
Note the change in dev tools setup when composing with middleware:
```js
// in store.js
import { createStore, applyMiddleware } from 'redux';
import catsReducer from './reducers/catsReducer';
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'

export const store = createStore(catsReducer, composeWithDevTools(applyMiddleware(thunk)))
```
***


### Give your app access to your store
We use `redux`'s `Provider` component to wrap our app, giving it the potential to access the store.
```jsx
// in index.js
import { Provider } from 'react-redux'

// EITHER 
import { store } from './store' // if defining store in another file
// OR
const store = createStore(catsReducer, devToolsEnhancer()); // if defining in the same file

ReactDOM.render(
    // wrap your app in the Provider component
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
);
```

***

# Read and write data from the store
Once your app is wrapped, any child components can now gain access to your store with the help of either [`connect`](https://react-redux.js.org/api/connect), or the Redux hooks. \


---

## Redux Hooks
If you are using React hooks in your project, we might as well be consistent and use the new(ish) Redux hooks too! We have `useSelector` for reading data from the global state, and `useDispatch` to get access to the dispatch method so we can dispatch actions to the reducer whenever we like. Usual hooks rules apply!

### Reading store data with `useSelector`
```jsx
import { useSelector } from 'react-redux';

function Doggos(){
    const theDogs = useSelector(state => state.doggos)
    const loading = useSelector(state => state.loading)

    const renderDoggos = () => theDogs.map(dog => <li>dog.name</li> />)

    return (
        <section id="doggos">
            { loading ? 'Loading ...' : renderDoggos() }
        </section>
    )
}

export default Doggos
```

### Dispatching actions with `useDispatch`
`useDispatch` exposes the `dispatch` function for us to use at anytime. Remember we can only dispatch actions (ie. POJOs with a key of `type`) so make sure to either pass one explicitly, or pass it an invoked function which returns an action object...

```jsx
import { useDispatch } from 'react-redux';
import { fetchDoggos } from './actions';

function FetchButton(){
    const dispatch = useDispatch();

    const handleClick = () => dispatch(fetchDoggos())

    return (
        <button onClick={handleClick} aria-label="refresh">    
            fetch!
        </button>
    )
}

export default App;
```

---
## connect() HOC
An alternative to using the `useDispatch` and `useSelector` hooks is to wrap your component in `connect` - a feature also from the Redux library. `connect can` receive up to 4 arguments, we will look at the first, most common, two. 
- a function that receives the global state and maps it to props
- another function or object which receives the dispatch method and also creates new props that trigger the dispatch.

#### Reading data with mapStateToProps
We are going to extract the parts of the data we need from the store and map them out as additional props for our component. To do this we will make a `mapStateToProps` function to pass as the first argument to connect. You can call this function whatever you like (I often shorten to `mSTP` but in docs and errors, it will refer to this first argument as `mapStateToProps`). Your mSTP function will receive the state object from the store. The keys of the object that your mSTP returns will be the names of your new props and the values of that object will be the value of that newly created prop.

```jsx
import { connect } from 'react-redux';

class CatsContainer extends Component {
    render(){
        {/* Thanks to mSTP, we can now access a prop called 'allCats' which hold the value of 'all' from our reducer */}
        return (<ul> { this.props.allCats.map(c => <li>{c.name}</li>)} </ul>)
    }
}

// Note this is defined outside the class
// Bear in mind if you have more than one reducer you will need to go via that layer eg. state.cats.all
const mSTP = state => ({ allCats: state.all })

export default connect(mSTP)(CatsContainer)
```

#### Writing data with mapDispatchToProps
The only way into our reducer should be via `redux`'s [`dispatch`](https://react-redux.js.org/using-react-redux/connect-mapdispatch) which will take in an argument of an action object and pass it along to the reducer. Remember those action creators we made earlier? Let's import one of them into our file and map it to a prop. For this we will use `connect`'s second parameter which expects either a function or an object. Either way, we refer to it as `mapDispatchToProps`. In it's function form, it receives the `dispatch` function as an argument and, like our mSTP, we will get our mDTP function to return an object of additional props we want to add to our component.

```jsx
import { connect } from 'react-redux';
// import an action creator (a funciton that returns an action object)
import { changeFavouriteCat } from '../actions/catActions';

class CatsContainer extends Component {
    render(){
        return (
            <ul> 
                <li onClick={() => this.props.selectAsFave(101)}>Zelda</li>
                <li onClick={() => this.props.selectAsFave(235)}>Tigerlily</li>
            </ul>
        )
    }
}

// Note this is defined outside the class
const mDTP = dispatch => ({ 
    // Here we use the action creator we imported
    selectAsFave: id => dispatch(changeFavouriteCat(id)),
    // Here we define an action object directly
    doARandomThing: () => dispatch({ type: 'RANDOM_EVENT' })
})

// Note that as we are not using mSTP here, the first argument is null
export default connect(null, mDTP)(CatsContainer)
```

If you are happy for the name of your action creator to also be the name of your prop, you can use the rather more pleasant destructured object option as the second argument to connect.
```js
import { connect } from 'react-redux';
import { changeFavouriteCat } from '../actions/catActions';

class CatsContainer extends Component {
    render(){
        return (
            <ul> 
                <li onClick={() => this.props.changeFavouriteCat(101)}>Zelda</li>
                <li onClick={() => this.props.changeFavouriteCat(235)}>Tigerlily</li>
            </ul>
        )
    }
}

export default connect(null, { changeFavouriteCat })(CatsContainer)
```

#### Read and Write
If you need both, your file could end up something like this:
```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { changeFavouriteCat } from '../actions/catActions';

class CatsContainer extends Component {  
    render(){
        const { allCats, faveCat, changeFavouriteCat } = this.props
        return (
            <>
            <ul> 
                { allCats.map(c => <li onClick={() => changeFavouriteCat(c.id)}>{c.name}</li>)}
            </ul>

            <p>{ faveCat ? `${faveCat.name} is your favourite!` : 'Select your favourite cat!' }</p>
            </>
        )
    }
}

// Note this is defined outside the class
// Bear in mind if you have more than one reducer you will need to go via that layer eg. state.cats.all
const mSTP = state => ({ 
    allCats: state.all,
    faveCat: state.favourite
})

export default connect(mSTP, { changeFavouriteCat }))(CatsContainer)
```

***

## BONUS: Async actions with [redux-thunk](https://www.npmjs.com/package/redux-thunk)
It would be great to have an action that makes eg. an API call before dispatching an action whose payload perhaps relies on the response. For this we will need a piece of middleware called [`redux-thunk`](https://www.npmjs.com/package/redux-thunk) which allows our action creators to return a function instead of an object. The function returned will receive redux's `dispatch` as an argument so we can manually decide when to dispatch (eg. only after an asynchronous event has completed).

To install run `npm install redux-thunk` and inject into your store as a piece of middleware (see example above).

That's it! Now you can return functions from your action creator functions if you like. 
```js
// in actions/catActions.js
export default addAllCats = () => {
    return dispatch => {
        fetch('https://awesomecatapi.com/cats')
            .then(resp => resp.json())
            .then(cats => dispatch({ type: "ADD_ALL_CATS", payload: cats }))
    }
};
```

It is considered good practice to fire off 'informational' actions to indicate in dev tools that the async behaviour has started. \
You might use this to eg. toggle a 'loading' key in your store.
```js
// in actions/catActions.js
export default addAllCats = () => {
    return aysnc (dispatch) => {
        try {
            dispatch({ type: "STARTING_FETCH" })
            const resp = await fetch('https://awesomecatapi.com/cats')
            const data = await resp.json()
            dispatch({ type: "ADD_ALL_CATS", payload: cats }))
        } catch (err) {
            dispatch({ type: "SET_ERROR", payload: err }))
        }     
    };
};

// in reducers/catsReducer.js
const initState = { allCats: [], loading: false };

const catsReducer = (state=initState, action) => {
    switch(action.type){
        case "STARTING_FETCH":
            return { ...state, loading: true };
        case "ADD_ALL_CATS":
            return { ...state, allCats: action.payload.cats, loading: false, error: false }
        case "SET_ERROR":
             return { ...state, loading: false, error: action.payload }
        default:
            return state
    }
}

export default catsReducer;

// in CatsContainer.js
import { fetchAllCats, changeFavouriteCat } from '../actions/catActions.js';

const CatsContainer = () => {
    const dispatch = useDispatch()
    const error = useSelector(state => state.error)

    useEffect(() => dispatch(fetchAllCats())

    return (
        <>
            { error && <div role="alert">{error.message}</div> }
        </>
    )
}

export default CatsContainer
```
