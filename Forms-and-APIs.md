_We can add a form to our page with the semantic element `<form>`. For each input in our form, we will need an `<input>`._

### Inputs
#### Input types
`<input>` tags have an attribute of "type" which declares what type of data that input expects. We can make use of some nice built-in input interfaces this way.
Note that "submit" is also an input type and will give you a submit button that triggers the form's action attribute (see below).
```html
<input type="text"/>
<input type="submit"/>
```
[Here is the list](https://www.w3schools.com/html/html_form_input_types.asp) of all the input types we have access to. Experiment and see what they do! \
Some personal favourites are `color`, `password`, `checkbox`...

#### Input placeholders
`<input>` tags also accept an optional attribute of `placeholder`. You can use it to show you user more info on what to enter. When they start interacting with the input, the placeholder will disappear.
```html
<input type="text" placeholder="Danearys"/>
```

#### Input names
`<input>` tags also accept an optional attribute of `name`. This attribute can be very useful if you want to reference a specific input in your form. Think of it as additional form of ID.
```html
<input type="text" name="name" placeholder="Danearys" />
<input type="text" name="title" placeholder="Mother of Dragons" />
```

There are more attributes, some that all input types can receive, some dedicated to specific input types. The documentation will be very useful when you want to dig further into these.

#### Input labels
Whilst we could get away without labels and use placeholders instead, unfortunately that approach would alienate some users who rely on accessibility tools. \
Another benefit is that clicking on the label will focus the matched input. \
To create an accessibility-friendly label, use the `<label>` tag with an attribute of "for" that points to the input's id attribute.
```html
<label for="name">Name:</label>
<input type="text" name="name" id="name" placeholder="Danearys" />
<label for="name">Name:</label>
<input type="text" name="title" id="title" placeholder="Mother of Dragons" />
```
Note that it's quite common to end up with both `name` and `id` attributes on an input that point to the same value. Both attributes can be very useful so it's usually not a case of 'choosing' between the two.

***

### Submitting a Form
#### action
As noted above, there is an input type of "submit". Clicking on a form's submit input will trigger its action. Generally this action involves the data being submitted to an endpoint and triggering a page redirect.
```html
<form action="/thankyou.html">
```
If no action is defined, it defaults to the current page but bear in mind that without intervention, this will trigger a page refresh.

#### eventListener
Alternatively, we could add an event listener to the form that reacts to the submit event.
```js
// in your .js script
const myForm = document.querySelector('#the-form');
myForm.addEventListener("submit", e => {
    e.preventDefault();
    const userName = e.target.name;
    const userTitle = e.target.title;
})
```
We're putting that event object to good use here - we are preventing the form's default submit action, avoiding an unwanted page refresh, and we're also using it to extract out certain values from the form using those `name` attributes we assigned to our inputs.

***

## APIs
Services that allow us to send and retrieve data are called APIs. This stands for Application Programming Interface. \
There are thousands of API’s out there that allow us to retrieve and use data from other services. Most of your favourite platforms likely have an API. \
You can retrieve your own GitHub data using [their API](https://developer.github.com/v3/) - check it out at `https://api.github.com/users/<your-github-username>`

### Keys
In some cases when we get data from an API it will be open and anyone can use it. In other cases we might need authentication. When we sign up for an account with most API’s we will receive an API key which personally identifies us with the API. Sometimes you will get both a public and a secret key. If you receive a secret key, you'll want to keep this server side so any cheeky user's can't use dev tools to find your secret key and start using your API quotas!

### Fetch
There are various 'Web APIs' which are available out-of-the-box in most browsers. In fact, the DOM is technically a Web API! One pretty cool one is the [Drag and Drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) See them all [here](https://developer.mozilla.org/en-US/docs/Web/API). The [***fetch*** API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) enables us to make requests and receive responses from other APIs. \
Let's use fetch to request that same GitHub single user endpoint.
```js
const url = "https://api.github.com/users/getfutureproof"
fetch(url).then(resp => resp.json()).then(console.log)
```
We are receiving a response, turning it into json format and then logging the result to the console.

As API calls tend to take a little bit of time, we want them to complete before we do the next action. This is why we use the .then() functionality. The `fetch` returns a Promise object which can have a status of `pending`, `fulfilled` or `rejected`. The `.then` is waiting for the Promise's status to have moved on from `pending`. You can (and should) add a `.catch` on the end of your chain so any `rejected` promises will be routed that way and your `.then`s will only receive `fulfilled` Promises.

#### Retrieving data
Let's put it all together to fetch some data from our GitHub API and append it to the DOM.

```js
const url = "https://api.github.com/users/getfutureproof"
fetch(url)
  .then(resp => resp.json())
  .then(addEmailToDom)
  .catch(err => console.warn('Opa, something went wrong!', err))  

function addEmailToDom(data){
  const footer = document.querySelector('footer');
  const mailLink = document.createElement('a');
  mailLink.setAttribute("href", `mailto:${data.email}?subject=Website Referral`);
  footer.append(mailLink)
}
```
You may have the option of adding query parameters to your url eg.
`https://api.thecatapi.com/v1/images/search?limit=25&order=desc`

#### Sending data
How about sending data to an API? We can use `fetch` again, we just need to change the request method to 'POST' and include the data we want to send. We do this in an optional 2nd argument to fetch which is an options Object. Usually the API's documentation will let you know what it needs to receive. This options Object is usually where we will need to authenticate ourselves as well.
```js
const url = "https://jsonplaceholder.typicode.com/posts"
const options = {
  method: 'POST',
  body: JSON.stringify({title: "Awesome Blog Post", body: "What a great day this was to make a new post", userId: 1})
}
fetch(url, options)
  .then(console.log("Posted post"))
  .catch(err => console.warn('Opa, something went wrong!', err))  
```
#### Other methods
As well as 'GET' (default) and 'POST', we may also find endpoints which respond to the verbs 'PUT', 'PATCH' or 'DELETE'. Generally, 'PUT' is for when we want to replace something, 'PATCH' is for when we want to update one part of something and 'DELETE' is self-explanatory.
***

### Fetch alternatives
fetch is pretty great - it's in-built and widely supported. There are alternatives to consider if you like however, including [axios](https://github.com/axios/axios) and [jQuery](https://api.jquery.com/jQuery.get/)

### Testing Tools
If you'd like to test out an API's endpoints before bringing them into your own code, [Postman](https://www.postman.com/) is a nice tool to use as an API testing playground.

***

# Resources
| [Forms docs](https://www.w3schools.com/html/html_forms.asp) | [Input Types](https://www.w3schools.com/html/html_form_input_types.asp) | \
| [fetch docs](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) | [Web APIs list](https://developer.mozilla.org/en-US/docs/Web/API)) | [GitHub API Docs](https://developer.github.com/v3/) | [Postman](https://www.postman.com/) |