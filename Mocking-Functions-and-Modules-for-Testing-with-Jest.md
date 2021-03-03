_Note: For mocking requests made with the `fetch` API, please see our [Mocking Fetch Requests for Testing](https://github.com/getfutureproof/fp_guides_wiki/wiki/Mocking-Fetch-Requests-for-Testing-with-Jest) guide._

The methods mentioned below are just the start of mocking with Jest! To dive deeper, check out the [official documentation](https://jestjs.io/docs/en/mock-functions).

## Mocking Functions
Let's say we have function we want to test that will receive an array and a callback. Something like:
```js
export function doThis(cats, cb){
    cats.forEach(cb)
}
```

A test for this function might assert something like 'it invokes the passed callback function once for every element in the given array'. In this assertion we don't really care what the callback function does, we just want to know that it is called. This is a perfect place to use a mock function.

```js
// in test file
const doThis = require('doThis')

describe('doThis', () => {
    test('it invokes the passed callback function once for every element in the given array', () => {
        const fakeCallback = jest.fn(); // create a mock function
        const testArray = ['this', 'that', 'the other'];
        doThis(testArray, fakeCallback);
        expect(fakeCallback).toHaveBeenCalledTimes(3);
    })
})
```

`toHaveBeenCalledTimes` is one of many methods that can be called on a mock function - it cannot be used on any old function so you can see how mocks can be very useful for tracking what is going on in your code!

## Mocking Modules
Often we make use of external modules/libraries in our application code. When testing, we don't usually need those library methods to actually run, usually we just want to make sure that they have been called and perhaps check to see what they were called with. For example, perhaps I want to test a method I've written that invokes the `.Order` method from the [`dominos` npm library](https://www.npmjs.com/package/dominos) (yes, that's a real thing!). I don't really want to make an order every time I test, I'd be inundated with pizza so instead we can tell jest to create a mock up of the library, which won't actually run the real `.Order` method!

### Create the mock
```js
// in test file
const dominos = require('dominos'); // we'll need to bring it the real library
jest.mock('dominos') // but we'll mock it immediately, no unexpected pizza here!
```

### Test a function that invokes the mocked library
Let's say we intend to have a function in our app code that invokes `dominoes.Order`. Something like:
```js
function pizzaPlease(pizzaType, size){
    const orderData = { pizza: pizzaType, size }
    dominos.Order(orderData) // please note this is NOT the actual usage of the dominos library!
}

module.exports = { pizzaPlease } // export function for test
```

We might write a test that invokes this function and checks to see that the `.Order` method from the dominos library was called.
```js
// in test file
const dominos = require('dominos');
jest.mock('dominos');

const helpers = require('../helpers'); // import the functions to test

describe('pizzaPlease', () => {
    test('it calls the dominos Order method', () => {
        const testPizza = 'margherita'
        const testSize = 'large'
        helpers.pizzaPlease(testPizza, testSize)
        expect(dominos.Order).toHaveBeenCalledWith({ pizza: 'margherita', size: 'large' })
    })
})
```

### We can use this with our own modules too!
```js
// in easterEgg.js
const helpers = require('./helpers')

const secretMessage(code){
    if(code === 'open sesame'){
        helpers.updateHeader('Come inside!')
    }
}

module.exports = { secretMessage }
```
```js
// in test file
const eggs = require('./easterEgg')
const helpers = require('./helpers')

jest.mock('./helpers')

test('secret message calls update header if correct code given', () => {
    eggs.secretMessage('open sesame')
    expect(helpers.updateHeader).toHaveBeenCalledWith('Come inside!')
})
```