## State
### Defining State
React class components can store local data in a `state` POJO (Plain Old JavaScript Object) class property. Only class components can use `state` in this way.

```jsx
class App extends Component {

  state = {
    alertMessage: "",
    readsCount: 0,
    stories: [
      { headline: 'Disaster Strikes', snippet: 'It was a dark and stormy night...'},
      { headline: 'Sunny Days Ahead', snippet: 'Even in the UK, beach days are still upon us.'},
      { headline: 'Beware the Frumious Bandersnatch', snippet: 'Twas brillig, and the slithy toves did fyre and gimble in the wabe.'}
    ]
  };

  render(){
    {/* Show this stuff */}
  };

};
```
*You can also define the state object in a custom constructor. See [here](https://github.com/getfutureproof/fp_guides_wiki/wiki/React-Props-and-Component-Lifecycle) for more info.*

### Reading from State
Reading from state is just like reading from any other POJO. This object is a property of the class so we will need to use `this.state` to access it.
```jsx
render(){
    <>
        <p>There have been {this.state.readsCount} readers!</p>
        <ul>
            {
                this.state.stories.map(st => <li><strong>st.headline</strong> {st.snippet}</li>)
            }
        </ul>
    </>
}
```

### Updating State
To make changes to state, **always use [`setState`](https://reactjs.org/docs/react-component.html#setstate)**.

Direct manipulation (eg. `this.state.message = "Hello!"`) is to be avoided at all times as this will not trigger React's [reconciliation](https://reactjs.org/docs/reconciliation.html) process.

This process is extremely useful for us as it gets React to queue up the changes we are requesting, make sure that they are executed in the most efficient way and help ensure that the changes are reflected on the virtual DOM accurately.

`setState` is a React.Component method that can receive either a POJO or a function as an argument.
#### If you do not reference state itself, pass an object
```jsx
    this.setState({
        alertMessage: "Goodbye, reader!"
    })
```
Note that the object we passed does not represent the entire state object. Only the keys we gave will be updated. The rest (in this case, `stories` and `readsCount`) will remain unchanged.

#### If you need to reference state itself, pass a function
```jsx
    this.setState(prevState => ({ 
        readsCount: ++prevState.readsCount 
    }))
```
This callback function will receive a snapshot of the state as it was at the time `setState` was called. This is very important because `setState` is an asynchrounous function and we cannot guarantee that another process will not make an update to state before this one. Make sure that when you reference state in your returned object, to use that snapshot, not `this.state`. In many documentations you will see that argument named simply `state` - I like to use `prevState` simply as an extra visual reminder!


***

## Eventing
Often we are updating state as a reaction to an event. Perhaps a user clicks a 'Like' button and we increase the number of likes. Maybe they submit a form with their username and we then display their username in a welcome message.

Just as we have a 'virtual DOM' or, an abstraction of the DOM (which itself is already an abstraction!), React takes the browser event that we know and love and abstracts it into a [SyntheticEvent](https://reactjs.org/docs/events.html).

Many of the event names in React will look very familiar, just camelcased: `onClick`, `onHover`, `onSubmit` for example.

We can add them to any element, passing a reference to the handler function we want to be triggered on that event. Note that we **do not** invoke the function at this point. If you do need to pass an explicit argument to your handler, pass an arrow function eg: `onClick={() => doThis(someArgument)}`

```jsx
class App extends Component {

  state = { readsCount: 0 };

  increaseReadsCount = () => this.setState(prevState => ({ readsCount: prevState.readsCount++ }))

  render(){

    return(
      <>
        <p>There have been {this.state.readsCount} reader(s)!</p>
        <button onClick={this.increaseReadsCount}>I've read!</button>
      </>
    )
  };

};
```

Just as with our 'regular' event handlers, we still get access to the event itself:
```jsx
handleFormSubmit = e => {
  e.preventDefault();
  this.setState({ username: e.target.nameInput.value });
};

// in render()
<form onSubmit={this.handleFormSubmit}>
    <input type="text" name="nameInput" placeholder="That's not my name!"/>
    <input type="submit" value="Update!"/>
</form>
```

***

## Controlled Forms
Because in React we are usually keeping track of the state of our local data, it is common practice to use a [controlled forms](https://reactjs.org/docs/forms.html#controlled-components) pattern. Using our SyntheticEvent of `onChange` in conjunction with `setState` and an `input`'s `value` attribute, this is nice and easy! In this example, I've made an agnostic handler that can be used with any input.
```jsx
state = { username: "", password: "" }

handleInput = e => {
    // using destructuring to access e.target.name and e.target.value
    const { name, value } = e.target;
    // note the use of the [square brackets] here so we can use the name variable (instead of accessing a key of .name)
    this.setState({ [name]: value }); 
};

// in render()
<form onSubmit={this.handleFormSubmit}>
    <input {/* You can multi-line if you like! */}
        type="text" name="nameInput" 
        placeholder="That's not my name!"
        value={this.state.nameInput} onChange={this.handleInput}
    />
    <input type="password" name="password" value={this.state.password} onChange={this.handleInput} />
    <input type="submit" value="Update!"/>
</form>
```

***

## Conditional Rendering
Conditional rendering is extremely useful. With our JSX tags we can inject our familiar JS logic such as the `&&` operator and ternary statements.
#### Using the `&&` operator
```jsx
{ 
    this.state.chosenStory &&
        <article id="feature"> {/* This should almost certainly be a separate component! */}
            <h3>{this.state.chosenStory.headline}</h3>
            <p>
            {this.state.chosenStory.snippet} Cat ipsum dolor sit amet...
            </p>
        </article>
}
```

#### Using a ternary statement
```jsx
<h3>Hi there, {this.state.name ? this.state.name : 'friend'}!</h3>
```