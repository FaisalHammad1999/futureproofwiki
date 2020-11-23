React Class Components come with a suite of Lifecycle methods to give us some control over the lifecycle of a component instance. \
In one lifecycle, in the same way that humans are born once, develop several times through life and pass on once, React Components are 'mounted' once, can be 'updated' multiple times and 'unmounted' once. We don't know if humans can have more than one lifecycle but React Components certainly can!

***NB: functional components can not access these methods.***

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