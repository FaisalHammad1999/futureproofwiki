This is a walkthrough for adding tests to a React project using [Jest](https://jestjs.io/) and [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) which is currently one of the most popular approaches. \
_If you are looking for a walkthrough using Enzyme instead of RTL, check out [our guide here](https://github.com/getfutureproof/fp_guides_wiki/wiki/Testing-React%3A-Jest-and-Enzyme)._

---

**A note on CRA**
`create-react-app` (CRA) generated projects need less setup but your file structure may not match the one given below (you can change it if you like!). The core dependencies for testing with Jest and RTL are included.

If you use CRA and are interested to see what is going on behind-the-scenes, I recommend initialising a CRA project and then running `npm run eject` to gain access to the scripts it is running for us.

---

### Non-CRA Setup
In a non-CRA project there is a little bit more setup but not too much. Check out the official configuration guide [here](https://jestjs.io/docs/en/tutorial-react).

**1. Install Dependencies**
```bash
npm install --save-dev jest babel-jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
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
- In it, make a file called `fileMock.js` with content eg. `module.exports="test-file-stub"` \
- Also make a file called `styleMock.js` with content eg. `module.exports={}`

***

## Configure Test Setup
### setup file
Create (or, in CRA, use the existing) `setupTests.js` file (I've put it in a `src/test` folder) and let's do some basic setup that we'll want to use across all our test files. If using CRA, clear out this file contents and start from scratch with us.

We'll start with React itself, we're sure to need that across the board!
```js
import React from 'react';
```

There are a couple of functions from these testing libraries that we may want to use across many, if not all, of our test suites - let's bring them in too.
```js
import { render } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
```

We will also import the jest-dom library to get access to more assertions
```js
import '@testing-library/jest-dom/extend-expect';
```

To make these available for the duration of our test runs, across all our test files.
```js
global.React = React;
global.render = render;
global.userEvent = userEvent;
```

Don't worry if you're not sure what these are for yet - all will be revealed below!

### test script
We want to make sure that this setup file is run each time we run our tests. CRA will do this for us is the file is at the top level of the `src` folder but if you want to store it elsewhere or if you are not using CRA, you can add the location using a flag on your test script: \
**In CRA**
```js
// in package.json "scripts"
"test": "react-scripts test --setupFilesAfterEnv ./src/test/setupTests.js"
```
**No CRA**
```js
// in package.json "scripts"
"test": "jest --watch --setupFilesAfterEnv ./src/test/setupTests.js"
```

## Testing a Component
Let's write tests for a basic App component. Jest is pretty smart and will pick up files which follow most standard test file naming conventions. I've decided to store my tests in a folder called `test` so I've created the file in there. My file structure currently looks like this:
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

Next we're going to use the `beforeEach` Jest hook to render the component for us before each test. Remember that `render` we imported from testing library and made globally accessible?

```js
import App from '../App.js';

describe('App', () => {
    beforeEach(() => {
        render(<App />)
    })
})
```

### A Mindset Shift
It is tempting to test each piece of inner workings of our components but this does not usually give a good simulation of how a user will interact with our application. When testing components, we want to replicate the user interaction as much as possible. This means selecting elements in ways users will find them and testing functionality by simulating user events.

As you look through this guide you will not see a demo of how to test a function that is declared within a component, nor anything on how to directly test what values are held in state. Instead, consider what the intended result is for your user, emulate their interactions and find elements in the way they will.

---

## Key React Testing Library Tools

### Selecting elements in the DOM with `screen` (& bonus accessibility enhancements!)
The `@testing-library/react` library offers a `screen` which we can use to visualise the resulting DOM tree. We can consider `screen` to be like our `document` when working with the original DOM. Just like `document`, `screen` also has methods available for selecting elements

**getByRole** \
`screen.getByRole` is a fantastic selector tool because it encourages us to implement excellent accessibility. Many HTML5 semantic elements have roles already assigned and we can manually add a `role` attribute to any html element eg. `<div role="feed"></div>` tells screen readers that this div contains infinite scroll content so it can adjust mode accordingly.

_See [this list of aria roles](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles)_

```jsx
import { screen, within } from '@testing-library/react';
{/* ... */}
    let news = screen.getByRole('feed')
    {/* will select eg. <div role="feed"></div> */}
    
{/* ... */}
    let heading = screen.getByRole('heading')
    {/* will select eg. <h1></h1> as all h? elements automatically get a 'heading' role */}
 
{/* ... */}
```

**getByLabelText** \
`screen.getByLabelText` is another great one to encourage accessibility. We can't always come up with a sensible role for each element but we can always label them. `getByLabelText` will select the element with a matching `aria-label` attribute value.

```jsx
let feature = screen.getByLabelText('featured story')
{/* will select the input element with the matching aria-label attribute*/}
<article aria-label="featured story"> 
```

`getByLabelText` can also select input elements by searching with the text content of its attached label!
```jsx
let nameInput = screen.getByLabelText('Username')
{/* will select the input element with the matching label */}
<label htmlFor="username">Username</label>
<input type="text" id="username" />
```

_There are many [other selector options](https://testing-library.com/docs/queries/about/). We recommend using these ones as a priority as it really encourages us to add these accessibility features in our resulting apps!_

---

**Selecting multiple elements** \
If more than one matching element is on the page, you'll need to use `getAllBy...`:
```jsx
let headings = screen.getAllByRole('heading');

{/* which will return an array of matching elements including anything like: */}
<h1>Greetings!</h1>
<h2>Subheader - I'm still a heading!</h2>
<p role="heading">I have been manually granted a role of heading!</p>

{/* remember to handle the array - eg. if you want the first matching you'll need to: */}
let firstHeading = screen.getAllByRole('heading')[0];
```

**Getting more specific** \
We can make our queries more specific with options objects:
```js
{/* To select an element with the role of 'heading' and the name attribute of 'headline' */}
const headline = getByRole('heading', { name: "headline" })
```

We can use another testing-library/react tools - `within` - to localise our search:
```js
import { screen, within } from '@testing-library/react';
{/* ... */}
    const firstStory = screen.getAllByRole('listitem')[0];
    const firstHeadline = within(firstStory).getByRole('heading', { name: "headline" });
{/* ... */}
``` 

---

### Making assertions on selected elements
Once we have used selectors to grab an element, we can interact with it just as any DOM element. Let's check that our `<h1>` has the word 'News' in it. I'm not fussed about if there are other words so I'll use the assertion of `.toContain`.

```js
test("has 'News' in the primary heading", () => {
    const heading = screen.getByLabelName("primary header")
    expect(heading.textContent).toContain("News");
});
```

How about checking the current styling on a selected element?
```js
test("shows the reader count in red", () => {
    const readerCount = screen.getByRole('figure');
    expect(readerCount.style.color).toBe("red");
});
```

---

### Simulate an event
Simulating an event couldn't be easier thanks to [`@testing-library/user-event`](https://testing-library.com/docs/ecosystem-user-event/)! Really, if you have experienced handling events with Enzyme and thought that was easy, this is next level intuitive!

```js
// Select an element you want to interact with
const theElement = screen.findByLabelName('the element');

// Wanna click on it?
userEvent.click(theElement)

// Wanna type in it?
userEvent.type(theElement, "Beth")

// Wanna type in it and then hit the enter key?
userEvent.type(theElement, "Beth{enter}")
```

_See [the documentation](https://testing-library.com/docs/ecosystem-user-event/) for all events (see the right hand side bar)_

Let's put it all together into a test:
```jsx
import { screen } from '@testing-library/react'; {/* Note this is a named imports */}
import userEvent from '@testing-library/user-event'; {/* Note this is not a named import */}
import TheForm from '../components/TheForm';

describe('TheForm', () => {

  beforeEach(() => {
    render(<TheForm />);
  });

  test("clears user input after submission", () => {
    const nameInput = screen.getByLabelText('Username')
    userEvent.type(nameInput, "Beth{enter}")
    expect(nameInput.value).toBe("");
  });

}
```

If you're wondering if we need to mock out things like prevent default, you'll be ecstatic to know that we do not! the user-event will handle all of that for us.

---

## Stubbing Out Props 
If the component you are testing is expecting props, you will need to pass it some fake ones for test purposes. We pass props to our test components the same way we would anywhere else:
```js
beforeEach(() => {
    const dogStub = { name: 'Mochi', age: 1 };
    render(<DogCard dog={dogStub}/>);
});
```

We can also pass fake functions and even test to see if they have been called upon, how many times, and with what arguments
```js
let likeDog = jest.fn();

beforeEach(() => {
    const dogStub = { name: 'Mochi', age: 1 };
    render(<DogCard dog={dogStub} likeDog={likeDog}/>);
})

test('it calls props.likeDog when clicking on like button', () => {
    const likeButton = screen.getByRole('button', { name: 'like' })
    userEvent.click(likeButton)
    expect(likeDog.mock.calls.length).toBe(1) // checks how many times likeDog was called
    expect(likeDog.mock.calls[0][0].toEqual('Mochi') // checks to see if likeDog was called with argument of 'Mochi'
}
```

---

## Running Your Tests
When running tests, adding the `--watch` flag will `watch` which means your test suite will run on every change. The usage instructions are wonderfully clear with your most commonly used commands likely to be `q` to quit watch mode, `a` to re-run all the tests and `f` to re-run only the failing tests.

***

## Displaying Test Coverage
For coverage we can create a new script in the `package.json`: \
**No CRA**
```js
"coverage": "jest --setupFiles ./src/test/setupTests.js --coverage --watchAll=false"
```
**CRA Setup**
```js
"coverage": "react-scripts test --setupFiles ./src/test/setupTests.js --coverage --watchAll=false"
```
This can be called with `npm run coverage` and will run the test suite, display the coverage and exit.
