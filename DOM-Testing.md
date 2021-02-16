It is said that one of the trickiest things to test in web development is code which manipulates the DOM. This is because the DOM we write for is that of the browser. Our tests are rarely running in the browser and therefore don't have a DOM (ergo a `document`) to work with. If you write a test for a function which references the `document`, then you are likely to get an error along the lines of `document is not defined`.

What we need to overcome this is a tool which can create a simulation of the DOM with which we can interact during our tests.

There are various tools available for this including Google's [Puppeteer](https://developers.google.com/web/tools/puppeteer), [Cypress](https://www.cypress.io/) and more.

As we have been using Jest quite a bit so far, let's look at Jest's offering which is actually built into the library.

## Setup
Since Jest ships with the tools needed, we don't need to install anything else.

In fact, all we really need to do, is let Jest know what HTML we require inside its simulated DOM for the purposes of our test(s). `document` is already defined within the scope of a Jest suite and we can set its `document.documentElement.innerHTML` to the required HTML string.


### HTML extracts
Even though you may have a lot of HTML, chances are that one test, or even one test file, only needs a small portion of it. Let's say we are testing a function (or functions) that only involve a ul and its contents on our HTML. We can give ourselves an li to play with too perhaps.

```js
beforeEach(() => {
    document.documentElement.innerHTML = '<ul id="food-list"><li id="item-1">tacos<span data-item-id="1">x</span></li></ul>';
})
```

### Importing HTML files
Okay so maybe you have a use case where you really need to just bring in an existing HTML file. We can only `require` JavaScript files so that's not an option. We'll need a bit of help from two built-in modules called 'fs' and 'path' to bring in the HTML file.

```js
const fs = require('fs');
const path = require('path');
const html = fs.readFileSync(path.resolve(__dirname, '../index.html'), 'utf8');


describe('index.html', () => {
    beforeEach(() => {
        document.documentElement.innerHTML = html.toString();
    })

    test('it has a header title', () => {
        let header = document.querySelector('header');
        expect(header.textContent).toContain('JavaScript in the Browser');
    })
})
```

### Writing a test that uses the `document`
As you can see in the example above, `document` now acts the same as the `document` we already know, we can use the usuals such as `querySelector`, `getElementById` etc.

Note that if you are importing code which references the document, you will need to do this after creating it.
```js
let modeHelpers; // if you require here, the file will not have access to `document`

describe('dark mode helpers', () => {
    let body;

    beforeAll(() => {
        document.documentElement.innerHTML = '<body><header><input id="dark-mode"></header>'
        body = document.querySelector('body')
        modeHelpers = require('../static/js/darkMode');
    })

    describe('switchMode', () => {
        test('it turns on light mode if box is unchecked', () => {
            modeHelpers.switchMode({ target: { checked: false }});
            expect(body.className).toBe('light');
        })
    })
})

```