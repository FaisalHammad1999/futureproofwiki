## Control Flow
### Comparison Operators
We use logic in our programming as points of decision, when our programme might take different routes. \
We evaluate which route to take based on different conditions. \
We expect a condition to have an outcome of true or false.  \
We have a range of operators that enable us to compare two values in order to get a boolean value so that we can then determine which path in our programme to take.
```js
> // greater than
< // less than
>= // greater than or equal to
<= // less than or equal to
```

We can compare if two things are equal using the double equals sign or check if two things are not equal using the not equal operator.
```js
== // equal to
!= // not equal to
=== // strictly equal to
!== // not strictly equal to
```

### Logical Operators
We might want to make this slightly more complicated and have two comparison statements that we need to be true, in this case we can combine them using a double ampersand.
```js
location === "USA" && age >= 21
```
Alternatively we might have a situation where we just need one condition to be true, in this case we use the double pipe operator.
```js
location === "UK" || age >= 21
```

### Ternary statements
Now that we have a mechanism to determine which route to take, let’s see how we can actually implement that in code. \
If we have a simple comparison to make we might want to use what is called a ternary.
```js
let age = 8;

age > 18 ? ‘child’ : ‘adult’
```
We often use ternary statements to check a value and return another under certain circumstances.

### If statements
We often want to do a bit more than one line once we have determined the path we are going to take based on our conditional.
In this situation, we can use an if statement.
```js
let number = 8;

if (number > 0) {
  return ‘positive’
}
```
As in the ternary, we might want a second option. We do this by adding an `else` statement.
```js
let number = 8;

if (number > 0) {
  return ‘positive’
}
else {
  return ‘negative’
}
```

Any numbers nerds reading may see a flaw here. We can use an `else if` statement to add more conditions to check before we hit the default `else`.
```js
let number = 8;

if (number > 0) {
  return ‘positive’
}
else if (number === 0){
  return ‘neutral’
}
else {
  return ‘negative’
}
```

### Switch Statements
All this could swiftly add up to be a lot of code! Luckily we have a third option in a switch statement. We can use this when we are checking the value of one item.
```js
switch (prompt("What is the weather like?")) {
  case "rainy":
    console.log("Remember to bring an umbrella.");
    break;
  case "sunny":
    console.log("Dress lightly.");
  case "cloudy":
    console.log("Go outside.");
    break;
  default:
    console.log("Unknown weather type!");
    break;
}
```
We can consider the default case to be akin to the else statement in an if statement. If will run if there are no matches. The difference is that a switch statement requires a `default` case but an if statement does not require an `else` statement.

***

## Loops
When we want to repeat a specific action until a condition is met, we can use a loop.

### While Loop
Consider this code. How many times will the loop run? Note the placement of the condition (`number >= 1 && number < 18`) and that if the condition is met, the code inside the curly braces `{ }` will get executed.
```js
let number = 0;

while (number >= 1 && number < 18) {
    console.log(`The num is ${number}`)
    number++
}
```

### Do Loop
The do loop is similar to a while loop, the only difference being that it is always executed at least once no matter what. \
If the condition of a while loop is never true, it will never be executed. \
Note the placement of the conditional ***after*** the block of code.
```js
do {
    console.log(`The num is ${number}`)
    number++;
} while (number >= 1 && number < 18);
```

### For Loop
We can use for loops where we have a task we want executed a specific number of times.
```js
for (let num = 0; num <= 18; num++) {
  console.log(num)
}
```

## Iterators
The difference between looping and iteration is subtle and you can deep dive into this courtesy of the Internet. As a high-level summary we might say that a loop allows you to do the same thing over and over again until a condition is met whilst an iterator allows you to start a sequence, repeating something based on the items in a collection.

### for ... in / for ... of 
for ... in and for ... of are newer tools but very handy ones! \
We us for ... in when working with objects and for ... of when working with arrays. (*Think 'for-in object'!*)
```js
let catData = { name: "Zelda", age: 3, markings: "calico" };

for (const key in catData) {
  console.log(`${key}: ${catData[key]}`);
}
```
```js
let cats = ["Zelda", "Rumble", "Sam"];

for (const catName of cats) {
  console.log(`This cat's name is ${catName}`);
}
```
One nice reason to use a for ... in / for ... of over a forEach (see below) is that you can add a `break` in a for ... in / for ... of to 'escape' the loop. This is not possible in a `forEach`.

### forEach
forEach is an oft-used iterator. It is only available for arrays. \
Like all of the following methods, forEach takes in a ***callback function***. Each item in the array is yielded to the function one at a time and the function executed. The return value of forEach is the array on which it was called.
```js
let cats = ["Zelda", "Rumble", "Sam", "Flora"];

cats.forEach(catName => console.log(`This cat's name is ${catName}`));
```

### map
map works in a similar way to forEach but the return values of each time the callback is executed, are returned in a new array.
```js
let cats = ["Zelda", "Rumble", "Sam", "Flora"];

cats.map(catName => catName.toUpperCase()); //=> ["ZELDA", "RUMBLE", "SAM", "FLORA"]
```

### some
some will run its callback function for each item in the collection until it hits one that makes the condition return true. At that point we can infer that at least 'some' of the items in the collection meet the condition and therefore can exit and return `true`.
```js
let cats = ["Zelda", "Rumble", "Sam", "Flora"];

cats.some(catName => catName.endsWith("a")); //=> true
```

### filter
filter's callback will also need to return a boolean value but this time it will be run for all items in the collection and any that make the condition return `true` will be placed into a new array that will become the return value of the `filter`.
```js
let cats = ["Zelda", "Rumble", "Sam"];

cats.filter(catName => catName.endsWith("a")); //=> ["Zelda", "Flora"]
```

### reduce
reduce is a notoriously tricky one to grasp but once you've got it, it can be very handy. It allows to keep a 'running total' of kinds (does not have to be numerical) and will pass it as an additional argument to the callback function to be operated on and returned. The final time, that running total becomes the return value of our `reduce`.
```js
const prices = [2.99, 4.75, 5.00, 12.99];
prices.reduce((runningTotal, nextValue); => runningTotal + nextValue) // 25.73
```
reduce can take an optional second argument of a custom 'starting point' for the running total. If no second argument is given, the first item will be used as the starting point.
As with all of the similar methods above, the callback can be defined elsewhere.
```js
const totalBill = 55;
const discountsToApply = [2, 5];

function applyDiscount(runningTotal, discount){
  return runningTotal - discount;
};

discountsToApply.reduce(applyDiscount, totalBill) //=> 47.01
```

## But wait, there's more!
If these aren't doing the trick for you, check out the documentation for the collection type you are working with and see if any of the other built in methods can help out!
