## Expressions and Functions
When we write lines of code, each line does a specific thing, it completes an action for us. \
We call a fragment of code that produces a value an ***expression*** but when we group these together we can call them a ***function***.
We create functions because often, to complete one action, we cannot do this in a one-liner expression.
Another reason to write a function is if we are writing the same code multiple times. If we find ourselves doing this we might group that code together and give it a name. This means our code is easier to read. Writing a function is a bit like writing a load of code, and giving it a name so that we can easily access it later. \
There is an art to naming functions - for now, keep it simple and descriptive.

## Types of Functions
There are 3 types of functions:
| In-built | Libraries | Custom |
### In-built
These are the actions that are so fundamental to a programming language they come built into its core. \
The best way to research in built functions is through the documentation. \
Each data type has a whole host of built in functions we can use with it - these are called methods.

### Libraries
These are actions that so many people want to use that developers have grouped together and created collections of code for us to reuse. \
Some well-known JavaScript libraries include...
- [moment](https://momentjs.com/) - a time and data parser
- [axios](https://github.com/axios/axios) (slight improvements to the fetch API flow)
- [GreenSock](https://greensock.com/) (for web animations)

And of course, the mother of controversy, bringer of memes... [jQuery](https://jquery.com/). It offers a lot but does get a lot of hate due to a past trend to use almost exclusively jQuery instead of 'vanilla' JavaScript. Even [Bootstrap](https://blog.getbootstrap.com/2020/06/16/bootstrap-5-alpha/) is dropping jQuery for v5. The truth is that many sites do still use it so it's worth having a look at it so you can identify and jump in when required but we highly recommend not using it in all (or even most) of your projects. jQuery's most-used features are DOM manipulation, ajax flow and some animation, although it does offer more.

### Custom
If a solution for the action you are trying to take in not built-in or available in a library, you'll need to make a custom function.

***

## Writing Functions
### Declaring a function
This is the syntax for a named function declaration.
```js
function myFirstFunction(){
 // do this stuff
}
```
Note the essentials:
- `function` keyword
- function's name
- parentheses
- open and close of the function block with `{ }`

### Invoking a function
Once a function has been declared, you can invoke (run) it by stating the name of the function and adding the invocation parentheses:
```js
myFirstFunction()
```

### Parameters & Arguments
Often, your function will need some kind of input in order to do its job. \
There is a subtle difference in the usage of these two words and only the strictest of humans would truly be mad at you for getting them mixed up: \
**Parameters** are variables you declare in a function definition. \
**Arguments** are the pieces of data you pass in when you invoke the function.
```js
function addUp (x, y) { // x is our parameter here
  console.log(x + y)
}

addUp(4, 5) // 4 and 5 are our arguments that will be assigned out to x and y as declared in our function definition
```

### Default Parameters
In some cases we might not need all of the parameters each time we run a function, therefore we might give it some defaults. \
```js
function addUp (x, y = 2) { // y has been given a default parameter of 2
  console.log(x + y)
}

addUp(7) // logs 9 to the console
addUp(7, 5) // logs 12 to the consol
```

## Return Statements
So far we have mostly been printing out data to our console once a function has done it’s work. In product terms though this isn’t very useful to our user as they are not often looking at the console. \
We often want a function to give us a value back once it has completed its job so we can go ahead and show our users that in whichever way we please. \
```js
function timesBySeven (x) {
  return x + 7
}

timesBySeven(2) //=> 14
```

We can now call that function in the same way but now we can store the output in a variable to be used at a later date
```js
const result = timesBySeven(2)
console.log(`The answer is ${result}.`)
```

If you like, you can even call the function in line. Be careful to keep it readable though.
```js
console.log(`The answer is ${timesBySeven(2)}.`)
```

## Arrow Functions
### Statements
There is another way to write out a function - with arrow notation. This notation was brought in during an update to Javascript but are not a replacement to traditional function declarations. Both have their place!

The only way to give a 'name' to an arrow function is by assigning it to a variable. 
```js
const myArrowFunction = () => { 
    console.log(“Something”)
}
```
You don't have to assign it to a variable however and a very common place to see arrow functions is when passing a callback function. It's just a bit neater!
```js
const cats = ["Zelda", "Rumble", "Sam", "Flora"];

cats.forEach(catName => { console.log(catName.toUpperCase())} )
```
As seen above, we can of course include paramaters in our arrow functions. They go inside the parentheses and if we only have one parameter we can get rid of the parentheses altogether. If you have no parameters, you must include the parentheses though!

Another thing to note about arrow functions is that they have implicit binding, so `this` cannot be reassigned. If that seems a bit mysterious right now, that's okay, we will be covering binding a bit later in the course!

### Expressions
You will almost certainly come across arrow functions without the `{ }` code block. These are function expressions and as a general rule, we expect statements to produce an action and expressions to produce a value.

To help us with that, when you ditch the `{ }` code block in an arrow function, we get an implicit return meaning that even without the `return` keyword, a value will be returned.
```js
const addUp = (x, y) => x + y
```
These are even more likely to be found as callbacks! Think about map where the return value is key!
```js
cats.map(catName => catName.toUpperCase() ) //=> ["ZELDA", "RUMBLE", "SAM", "FLORA"]
```
Beware, if you are looking to return an Object from a function expression, you need to wrap it in `( )` parentheses so the function doesn't try and read it as a code block! 
```js
cats.map(catName => ({ name: catName, loudName: catName.toUpperCase() }) ) //=> [{name: "Zelda", loudName: "ZELDA"}, {name: "Rumble, loudName: "RUMBLE", ...etc]
```

## Recursion
Can we call a function from within itself? Absolutely! This is recursion.
```js
function getFizz(){
    let random = Math.floor(Math.random() * 30);
    console.log(random);
    if (random % 3 === 0) {
        console.log('Found a fizz!');
    } else {
        console.log('Hmm, keep trying...');
        getFizz();
    }
}

getFizz() // What will happen here?
```

## Closures
To quote MDN: *"A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function’s scope from an inner function."*

What will happen in this code? Why does this work?
```js
function matilda(multiplier){
  return (x => x * multiplier)
} // What does the matilda function return?

const timesByEight = matilda(8) // What value has timesByEight been assigned here?
const eightByThree = timesByEight(3) //=> 24

const timesByTen = matilda(10) // What value has timesByTen been assigned here?
const tenByThree = timesByTen(3) //=> 30
```