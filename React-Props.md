Passing data between parent and child components in a React app can be achieved with React Props (properties). \

### Passing props
Let's say we have some TV show data stored in state that we want to pass to a ShowCard component.
```jsx
const [ faveShow, setFaveShow ] = useState({title: 'The Good Place', seasons: 4})

// in return
    <ShowCard show={faveShow}/>
```

### Receiving props
In our `ShowCard` component, we can access the received props in one of two ways, depending on whether it is a class or a functional component:
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

### Callbacks as props
As we know, functions can be treated in the same way as other data types in JavaScript. We can pass functions as props:
```jsx
function App() {
    const doSomething = () => console.log('Doing something!');

     return <LikeButton clickHandler={doSomething} />
};

const LikeButton = ({ clickHandler }) => <button onClick={clickHandler}>Click to do something!</button>
```
