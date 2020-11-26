## Enabling use of async/await
This is only needed if you run into the error of `regeneratorRuntime is not defined`

`npm install --save-dev @babel/plugin-transform-runtime`

```js
// in your babel config file
{
    "presets":[
        "@babel/preset-env",
        "@babel/preset-react"
    ],
    "plugins": [
        "@babel/plugin-proposal-class-properties",
        "@babel/plugin-transform-runtime" // here is the new one
    ]
}
```

## Install and configure jest-fetch-mock
`npm install --save-dev jest-fetch-mock`

```js
// in your test setup file
import jestFetchMock from 'jest-fetch-mock';
jestFetchMock.enableMocks()
```

## Write a test for a function that calls fetch
```js
it('does something', async (done) => {
    let fakeResponse = ['img1', 'img2']
    fetch.mockResponse(JSON.stringify(fakeResponse))
    await somethingAsync()
    expect(something).toEqual(something)
    done()
})
```