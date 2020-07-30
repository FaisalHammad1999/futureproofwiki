This is a walkthrough to adding tests to a `create-react-app` (CRA) generated project using [Jest](https://jestjs.io/), [Enzyme](https://enzymejs.github.io/enzyme/) and [Sinon](https://sinonjs.org/). These tools are not specific to React and there are many alternative options.

## Install Dependencies
In a CRA project, Jest is already installed and configured. For this walkthrough we will need only to run:
```bash
npm install enzyme enzyme-react-adapter-16 sinon
```

In a non-CRA project there is a little bit more setup but not too much. Check out the official configuration guide [here](https://jestjs.io/docs/en/tutorial-react). If you use CRA and are interested to see what is going on behind-the-scenes, I recommend initializing a CRA project and then running `npm run eject` to gain access to the scripts it is running for us.

## Configure Setup
### setup file
Create (or use the existing) `setupTests.js` file and let's do some basic setup that we'll want to use across all our test files.

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
And finally, we'll likely be using sinon in multiple places across our test suites so let's include it in our overall setup.
```js
import sinon from 'sinon';
```

We'll only be using `configure` and `Adapter` in this file. The others we'll want to make available for the duration of our test runs, across all our test files.
```js
global.React = react;
global.shallow = shallow;
global.sinon = sinon;
```

### test script
We want to make sure that this setup file is run each time we run our tests. CRA will do this for us is the file is at the top level of the `src` folder but if you want to store it elsewhere or if you are not using CRA, you can add the location using a flag on your test script:
```js
// in package.json "scripts"
"test": "react-scripts test --setupFiles ./src/test/setupTests.js"
```

## Testing a Component
Let's write tests for a basic App component. Jest is pretty smart and will pick up files which follow most standard test file naming conventions. We'll go with the CRA default of App.test.js. I've decided to store my tests in a folder called `test` so I've moved it into there. I've also got rid of everything CRA added for me so we'll start from scratch. My file structure currently looks like this:
```
-/myApp
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