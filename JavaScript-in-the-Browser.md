## The DOM
The ***Document Object Model*** is a representation of the HTML document that is received by a browser when a webpage has been requested. \
Since this is merely a representation, we are free to make changes to it without actually altering the HTML. \
We can also consider this to be a level of abstraction - the DOM being an abstraction of some text. This will become more of a talking point when we get into React!

## Loading your JavaScript
If you only need a tiny piece of JS and you don't want to make a file for it, you can also write JS within `<script>` tags. As you can imagine, you don't want this to get out of hand so try not to overuse this.
```html
<script>
  let date = new Date();
  alert(`The date is ${date}`)
</script>
```

You are more likely to want to keep your JavaScript in a separate file. To load a JavaScript file into your HTML, you can use a `<script>` tag and point the `src` attribute to your file.
```html
<script src="index.js"></script>
```
You have options when considering where to place this tag and the answer to 'where is best?' leads us to the question 'when do you need it to be loaded?'. \
- In the `<head>`: scripts will be loaded before HTML
- End of the `<body>`: scripts will be loaded after HTML
Loading your scripts at the end of the body can result in a quicker initial 'paint', but does mean that you don't have access to it until after the HTML is fully loaded so bear in mind your needs.

***

## Selectors
The first thing we need when we think about making updates or listening to the DOM is to which what part of our HTML we are referring. \
We can think of this similarly to how when we make a new CSS rule, we first have to think of which selector we will use to 'grab' the appropriate element. \
We can grab elements from the `document` with a variety of methods...
### querySelector
querySelector takes CSS-style selectors (you can chain as well) and returns the ***first matching*** element.
```js
document.querySelector("#help")
document.querySelector("h1")
document.querySelector(".error")
```
### querySelectorAll
querySelectorAll is the same as querySelector but returns a [***NodeList***](https://developer.mozilla.org/en-US/docs/Web/API/NodeList) with all the matching elements. A NodeList is similar to an array but does not share all of the same in-built methods as an array does. If you want to turn a NodeList into an array, you can use `Array.from()`, passing the NodeList as an argument.
### getElementById
getElementById does what it says on the tin - bear in mind that since IDs are intended to be unique, this returns just one element. Note that you do not need to add the `#` selector in your argument.
```js
document.getElementById('logo')
```
### getElementsByClassName
getElementsByClassName is also self-explanatory. Since class names are *not* designed to be unique, this returns an HTMLCollection(https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection), even if it finds only one matching element. An HTMLCollection is very much like a NodeList. Note that you do not need to add the `.` selector in your argument.
```js
document.getElementsByClassName('card-text')
```
### getElementsByTagName
getElementsByTagName fetches all elements of the matching html tag. This returns an HTMLCollection, even if it finds only one matching element. 
```js
document.getElementsByClassName('card-text')
```

***


## Events
As users interact with our websites they will be triggering various ***events***. \
Examples of events include clicking on a button, hovering or scrolling. \
There are also events that are not initiated by the user, these are the ones that come from the website itself, one example of this could be the website being fully loaded. \
We can use events to start a chain of updates to our website.

Once we have selected a target element, let's store it in a variable so we can access it later.
```js
const helpButton = document.querySelector('#help)
```
Then we can call the method `addEventListener` on our element to give it some interaction response.
### addEventListener
addEventListener takes 2 arguments, an event that we are listening for and a callback function which takes in the event and defines the reaction we want to trigger when that event is triggered.
```js
help.addEventListener("click", e => { console.log("I was clicked!" })
```

### Binding
When our Javascript file is loaded and it sees a new event listener it tries to attach that listener to everything it already knows in our HTML. This attachment is called binding.
What would happen if not all of our HTML has been loaded yet? The listener would attempt to bind but the HTML element may not exist yet. To avoid this, you can use a couple of different event listeners, bound to the document itself.
#### DOMContentLoaded 
The DOMContentLoaded event fires when the initial HTML document has been completely loaded and parsed. It does not guarentee that any styling, images or subframes have completed loading.
`document.addEventListener("DOMContentLoaded", () => { // start doing other bindings now })`
#### load 
The load event fires when a page and all its assets are 100% fully loaded. Don't use this unless you really mean to.
`document.addEventListener("load", () => { // do stuff }`

**Or...**
#### defer
defer is a boolean attribute you can apply to any `<script>` tag which also has a `src` attribute.
The file it links to will run after the HTML is parsed but before "DOMContentLoaded" is fired.
```html
<script defer src="js/main.js"></script>
```

### Event types
[Here is the full list](https://developer.mozilla.org/en-US/docs/Web/Events) of events you can listen for - there are plenty! Some of the most common ones include: \
| click | keydown | focus | blur | hover | mouseenter | mouseleave | ... endless possiblities!

### The Event Object
In the addEventListener example above, we see that the callback receives 'e' (call anything you like but `e` is a popular convention). This will be the ***event object*** which we can use to gather more information from the event. Some elements have default reaction baked into them - if you would like to override that behaviour, you can state `e.preventDefault()` in your callback's function body.

***

## Updating our HTML
We can update our HTML in a selected element with the following methods:
### innerHTML
innerHTML will replace the HTML inside the selected element with whatever we assign it to.
```js
const header = document.querySelector('header');
header.innerHTML = "<h1>Hello JavaScript!</h1>";
```
Avoid using this to insert just text content, especially if it has come from you user! `innerHTML` will parse the string as HTML so leaves you open to injection attacks.
### textContent
textContent is the appropriate way to update the text in an element.
```js
const alertBanner = document.querySelector('#alert');
alertBanner.textContent = "Don't do that!";
```
### append
append let's you add to the content of an element without overwriting what is already there.
```js
const alertBanner = document.querySelector('#alert');
alertBanner.append("You fool!");
```
### createElement
createElement let's us spin up a brand new element with using an HTML string. Let's use it in combination with some of our other methods to add a new list item to an existing ul.
```js
const myList = document.querySelector('#things');
const newItem = document.createElement('li');
myList.append(newItem);
```

***

## Updating attributes
### setAttribute
setAttribute accepts two arguments, one is the name of the attribute you wish to change or set, the second is the value you wish to set it to. You can use it to set any attribute.
```js
const profileLink = document.querySelector('#profile');
profileLink.setAttribute("href", "http://getfutureproof.co.uk/")
```

***

## Updating our CSS
### style.property
We can access and update a CSS rule for a selected element via the `style` method. Note that where a property would have a `-`, it is camelCased.
```js
const alertBanner = document.querySelector('#alert');
alertBanner.style.color = "red";
alertBanner.style.fontWeight = "bold";
```
### setAttribute
Alternatively you can use the the `setAttribute` method, giving "style" as the first argument. Note that the second argument is a string of 'regular' CSS rules.
```js
alertBanner.setAttribute("style", "color: red; font-weight: bold"
```

***

# Testing
Testing the DOM has a reputation of being tricky! Check out our guide to [DOM Testing](https://github.com/getfutureproof/fp_guides_wiki/wiki/DOM-Testing) to find some good solutions!