There are several libraries to assist with making HTTP requests, one of the most popular being `axios`. To test requests being made with axios or similar, you can [mock the library](https://github.com/getfutureproof/fp_guides_wiki/wiki/Mocking-Functions-and-Modules-for-Testing-with-Jest). You may find yourself, however, wanting to test a request made using the browser's `fetch` API. For this type of testing, we can use a library such as `jest-fetch-mock` which we will walk through below (note that there is another library, `fetch-mock-jest` which solves the same issue just with different implementation).

## Setup
### Install and configure jest-fetch-mock
`npm install --save-dev jest-fetch-mock`

```js
// in your test file or test setup file
global.fetch = require('jest-fetch-mock');
```

### Start from a clean slate
We want to ensure that we are starting from the same point with each test. It is good practice to clear out all your mocks before each test. In Jest, a `beforeEach` is a good place for it.
```js
beforeEach(() => { fetch.resetMocks() })
```

## Writing tests
### Test to see if fetch was called
```js
// In this example `githubHelpers` is an imported module
test('it makes a fetch call to the given user\'s github api url', async () => {
    await githubHelpers.getGitHubUserInfo('gingertonic')
    expect(fetch).toHaveBeenCalled()
})
```

### Test to see what was passed to a fetch call
```js
test('it makes a fetch call to the given user\'s github api url', async () => {
    await githubHelpers.getGitHubUserInfo('gingertonic')
    expect(fetch).toHaveBeenCalledWith('https://api.github.com/users/gingertonic')
})
```

### Test to see what happens to the response data on success
```js
test('it makes a fetch call to the given user\'s github api url', async () => {
    fetch.mockResponse(JSON.stringify({ "public_repos": 100 }))
    const returnVal = await githubHelpers.getGitHubUserInfo('gingertonic')
    expect(returnVal).toBe(100)
})
```

### Test to see what happens to the response data on failure
```js
test('it makes a fetch call to the given user\'s github api url', () => {
    fetch. mockReject(new Error('Fake disaster'))
    const returnVal = githubHelpers.getGitHubUserInfo('gingertonic')
    expect(returnVal).toBe('Oh no! Fake disaster!')
})
```