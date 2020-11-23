This is a walkthrough for adding unit tests to a React project using [Jest](https://jestjs.io/), [Enzyme](https://enzymejs.github.io/enzyme/) and, optionally, [Sinon](https://sinonjs.org/). These tools are not specific to React and there are many alternative options. `create-react-app` (CRA) generated projects need less setup but your file structure may not match the one given below (you can change it if you like!)
***
### CRA Setup
**Install Dependencies** \
In a CRA project, Jest is already installed and configured. For this walkthrough we will need only to run:
```bash
npm install --save-dev enzyme enzyme-adapter-react-16 sinon
```
If you use CRA and are interested to see what is going on behind-the-scenes, I recommend initializing a CRA project and then running `npm run eject` to gain access to the scripts it is running for us
***
### Non-CRA Setup
In a non-CRA project there is a little bit more setup but not too much. Check out the official configuration guide [here](https://jestjs.io/docs/en/tutorial-react).

**1. Install Dependencies**
```bash
npm install --save-dev jest babel-jest enzyme enzyme-adapter-react-16 sinon
```

**2. Add Jest config to handle static assets (css and other files)**
Add config to package.json
```js
// in package.json
{
  // etc
  "jest": {
    "moduleNameMapper": {
      "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/__mocks__/fileMock.js",
      "\\.(css|less)$": "<rootDir>/__mocks__/styleMock.js"
    }
  },
  // etc
}
```

**3. Create mock files referenced in the above setup**
- Create a new folder at the top level of your project called `__mocks__` \
- In it, make a file called `fileMock.js` with the content `module.exports="test-file-stub"` \
- Also make a file called `styleMock.js` with the content `module.exports={}`

***

## Configure Test Setup
### setup file
Create (or, in CRA, use the existing) `setupTests.js` file (I've put it in a `src/test` folder) and let's do some basic setup that we'll want to use across all our test files. If using CRA, clear out this file contents and start from scratch with us.

We'll start with React itself, we're sure to need that across the board!
```js
import React from 'react';
```
Next let's add some very short setup which allows us to use Enzyme seamlessly with React.
```js
// in setupTests
import { configure, shallow } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```
And finally, if you'd like to use sinon across our test suites, let's include it in our overall setup.
```js
import sinon from 'sinon';
```

We'll only be using `configure` and `Adapter` in this file. The others we'll want to make available for the duration of our test runs, across all our test files.
```js
global.React = React;
global.shallow = shallow;
global.sinon = sinon; // optional, if using sinon library
```

### test script
We want to make sure that this setup file is run each time we run our tests. CRA will do this for us is the file is at the top level of the `src` folder but if you want to store it elsewhere or if you are not using CRA, you can add the location using a flag on your test script: \
**In CRA**
```js
// in package.json "scripts"
"test": "react-scripts test --setupFiles ./src/test/setupTests.js"
```
**No CRA**
```js
// in package.json "scripts"
"test": "jest --watch --setupFiles ./src/test/setupTests.js"
```

## Testing a Component
Let's write tests for a basic App component. Jest is pretty smart and will pick up files which follow most standard test file naming conventions. We'll go with the CRA default of App.test.js. I've decided to store my tests in a folder called `test` so I've moved it into there. I've also got rid of everything CRA added for me so we'll start from scratch. My file structure currently looks like this:
```
-/myApp
    -/__mocks__
        -fileMock.js
        -styleMock.js
    -/node_modules
    -/public
    -/src
        -/test
            -App.test.js
            -setupTests.js
        -App.js
        -index.js
    -package.json
    -package-lock.json
```

### Import the relevant file
We'll need to access whichever file holds the code we're testing.
```js
// in App.test.js
import App from '../App.js';
```

### Declare your intentions!
This will look familiar to most people who have used almost any testing tools - we start with Jest's version of `describe`:
```js
describe('App', () => {
    // test things here!
})
```

Next I'm going to declare a few things that I'll be referencing multiple times. We'll definately be referencing the App component throughout this file so let's declare that. We'll also be testing the behaviour of a form and an input. The danger here is that the effects of one test are left hanging over into the next. We will circumvent that by using Jest's `beforeEach` method which will run before each individual test that we run.

```js
describe('App', () => {
    let component, form, nameInput;
    // You will usually find `component` name `wrapper` in documentation.
    beforeEach(() => {
        component = shallow(<App />);
        form = component.find('form');
        nameInput = component.find('#nameInput');
    })
})
```
Above we have used Enzyme's `shallow` to dictate the level at which we need access to this component. `render` and `mount` are alternative option if you need deeper access.

Note also how Enzyme's `find` method takes arguments in a similar style to `document.querySelector`: (`('tagName')`, `('#id')` or `('.class')`)
***
## Anatomy of a Jest test suite
In a `describe` block, multiple `test`s can be defined:
```js
describe('App', () => {
    beforeEach(() => {
       // some pre-test setup
    });

    test("what test 1 tests for", () => {
        // how we test it
    });

    test("what test 2 tests for", () => {
        // how we test it
    });
})
```
***
## Enzyme assertions
### Test that text has rendered
Once we have used `find` to grab an element, we can access its text content with `.text()`. Let's check that our `<h1>` has the word 'News' in it. I'm not fussed about if there are other words so I'll use the assertion of `.toContain`. `.toBe` and `.toEqual` are possible alternatives here depending on how specific you want to be.
```js
test("has 'News' in the title", () => {
    expect(component.find('h1').text()).toContain("News");
});
```

### Simulate an event
Simulating an event couldn't be easier thanks to Enzyme's handy `simulate` method!
```js
component.find('button').simulate('click');
```

Let's use `simulate` to fake a user adding text input. I know that my handler function that I passed to my `onChange` for this element receives the event `e` as an argument and then accesses `e.target.name` and `e.target.value`. Since `simulate` offers a fake event, we'll have to stub out the content of that event and pass it as the second argument.
```js
nameInput.simulate("change", {target: {name: "nameInput", value: "B"}});
```

When testing a form submission, it is likely your handler function will run a `preventDefault` on the incoming event. In that case we'll need to stub out that method too. You might be reusing this so consider making it accessible more widely than a single test.
```js
const basicFakeEvent = { preventDefault: () => "do nothing" };

form.simulate("submit", basicFakeEvent);
```

### Test values in state
To access state you can call directly on your variable that stores your `shallow` render. Let's do that in conjuction with a change simulation, testing a controlled form input:
```js
test("updates state when a user enters input", () => {
    nameInput.simulate("change", {target: {name: "nameInput", value: "B"}});
    expect(component.state('nameInput')).toBe('B');
});
```

### Testing an element's props
You can access the `props` object of any element using Enzyme's `props()` method:
```js
test("clears user input after submission", () => {
    nameInput.simulate("change", {target: {name: "nameInput", value: "Beth"}})
    form.simulate("submit", basicFakeEvent);
    expect(component.find('#nameInput').props().value).toBe("");
});
```

***
## Accessing Components with wrappers
Sometimes we wrap components for example with `react-router-dom`'s `withRouter` or `react-redux`'s `connect`. \
This causes a bit of confusion for enzyme but it is easily remedied. When setting up for a wrapped component, just say so when doing your `shallow` render by using `ComponentName.WrappedComponent`:
```js
let component
beforeEach(() => {
    component = shallow(<Things.WrappedComponent />);
})
```
***
## Stubbing Out Props 
If the component you are testing is expecting props (either from trickle down or via mSTP/mDTP), you will need to pass it some fake ones for test purposes. We pass props to our test components the same way we would anywhere else:
```js
let component;
beforeEach(() => {
    const dogStub = { name: 'Mochi', age: 1 };
    component = shallow(<DogCard dog={dogStub}/>);
});
```

We can also pass fake functions and even test to see if they have been called upon, how many times, and with what arguments
```js
let component;
let likeDog = jest.fn();
beforeEach(() => {
    const dogsStub = [{ name: 'Mochi', age: 1 }, {name: 'Masha', age: 7}];
    component = shallow(<DogCard dog={dogStub} likeDog={likeDog}/>);
})

test('it calls props.likeDog when clicking on like button', () => {
    const likeButton = component.find('.likeButton').first() // get the first element with a class of 'likeButton'
    likeButton.simulate('click')
    expect(likeDog.mock.calls.length).toBe(1) // checks how many times likeDog was called
    expect(likeDog.mock.calls[0][0].toEqual('Mochi') // checks to see if likeDog was called with argument of 'Mochi'
}
```
***

## Spying on custom class methods with Sinon
Let's say we want to check to see if one of our custom methods were called when we click on an element. My app is set up so all the `stories` in state are rendered as an `<li>`. I'll start by manually updating state so I can be 100% certain what is in it and then grabbing the first li on the page.
```js
component.setState({ stories: [ { id: 2503, headline: 'Disaster Strikes', snippet: 'It was a dark and stormy night...'} ] });
const story1 = component.find('li').first();
```
Next to set up the spy which will be checking our component's `handleStorySelect` method. To access this, we will need to create an actual instance of our component before extracting the method and creating a spy.
```js
const instance = component.instance();
const handleStorySelect = sinon.spy(instance, 'handleStorySelect');
```
Now we have everything we need, let's simulate clicking on the li:
```js
story1.simulate('click');
```
And now we can make our assertions. Clicking on the li should have called `handleStorySelect` once and given the data I manually set at the start of the test, I expect the id of `2503` to have been passed as an argument.
```js
expect(handleStorySelect.calledOnce).toBe(true);
expect(handleStorySelect.calledWith(2503)).toBe(true);
```
Put it all together:
```js
test("clicking on a story triggers a handleStorySelect function", () => {
    component.setState({ stories: [ { id: 2503, headline: 'Disaster Strikes', snippet: 'It was a dark and stormy night...'} ] });
    const story1 = component.find('li').first();
    const handleStorySelect = sinon.spy(instance, 'handleStorySelect');
    story1.simulate('click');
    expect(handleStorySelect.calledOnce).toBe(true);
    expect(handleStorySelect.calledWith(2503)).toBe(true);
});
```
***

## Running Your Tests
When running tests, the script CRA uses (or adding the `--watch` flag on a non-CRA script) will `watch` which means your test suite will run on every change. The usage instructions are wonderfully clear with your most commonly used commands likely to be `q` to quit watch mode, `a` to re-run all the tests and `f` to re-run only the failing tests.

***

## Displaying Test Coverage
For coverage I have set up a new script in the `package.json`: \
**CRA Setup**
```js
"coverage": "react-scripts test --setupFiles ./src/test/setupTests.js --coverage --watchAll=false"
```
**No CRA**
```js
"coverage": "jest --setupFiles ./src/test/setupTests.js --coverage --watchAll=false"
```
This can be called with `npm run coverage` and will run the test suite, display the coverage and exit.
