## Numeric
- Numbers can be **integers** (whole) or **floats** (numbers with a decimal point in them).
- Javascript has built in operators we can use on numeric data such as addition, multiplication, divisibility, modulus etc.
- Whilst we are doing some mathematics we might create another type of numeric value called a **NaN** (Not a Number)
- Any very large number (over 2<sup>53</sup> - 1) are considered to be a **bigint**.
***
## Alpha-numeric
Alpha-numeric data is a collection of characters that form words, a sentence, a paragraph etc. In JavaScript (and in wider programming) we call these **strings**.
- Strings can contain numbers, but they can also only be made up with numbers.
- To signify a string we place "quotes" around it. `["", '', ``]`
If we want to include a quote in our string we usually have to `\` escape it. Escaping a character lets JavaScript know that it should consider the following character as a character in a string, not the end of a pair of quotation marks.
```js
return "And then I said \"No thank you\"" //=> "And then I said "No thank you"
```
- There are also other escape characters that enable us to add special meaning into our strings. For example if we want to add a tab we include `\t` in our string or a new line we add `\n`.
- Just as we have operations on our numeric data, we also have operations on our string data. One of these is concatenation, which is the joining of two strings and is implemented using a `+`.
***
## Boolean
- Boolean data is either **true** or **false**
***
## Symbol
- Symbols are unique values, not equal to anything else. It is unlikely you will encounter many symbols in JavaScript but keep an eye out if you see them used in documentation.
```js
const da = Symbol("da")
da //=> Symbol(da)
```
***
## Objects
Objects are a bit different because they can combine all of the other data types. There are multiple types of Objects - there are some that are predefined in Javascript and we can also create our own.
### Arrays
An **array** is a signified by square brackets. Arrays have an order and therefore we can access different items in the array by asking for the index at which is is placed. They are indexed from 0.
```js
const myArray = ["one", 2, {age: 3}];

myArray[0] //=> "one"
```
### Objects
Yep, we know, that's confusing. You could also consider this as a 'dictionary'-style object. They are signified by curly braces. These type of objects do not have an order but are made of `key: value` pairs. If we want to access a value we must look it up via it’s key.
```js
const myObject = { name: "Ada", age: 37 };

myObject.age //=> 37
```
### Functions
In JavaScript functions are "first-class objects" which means they can be treated the same way as the other data-types eg. stored in a varable or in a data collection.
```js
function myFunc(name, age){
 return `${age}? Why, ${name}, you don't look a day over ${age + 10}!`;
};

const anotherFunc = () => console.log("Hello World!");

const theseThings = ["Ada", myFunc, 92, { colour: "orange" }, anotherFunc ];

theseThings[1]("Beth", 32) //=> "Why, Beth, you don't look a day over 42!"
```
***
## Lack of Data
There are two types in Javascript that signify the lack of data. 
- **Null** means there is no value. This usually occurs when we try to access something which has not been defined. 
- **undefined** is where we have let Javascript know we want a value but we haven’t actually put any data in it yet. 
***

# Variables
It makes sense for us to sometimes store a bit of data against a name so that we can access it again later. This is what’s called a variable. When we create a variable in Javascript we first have to make a decision around if this variable is going to change throughout our program. 
- If we think the value of our variable will change we should begin to declare the variable with the word `let`. 
- If we think the value of our variable won’t change we should begin to declare the variable with the word `const`. 
- We also have the option to declare a variable using the word var, this is not current practice but it is likely you will see this elsewhere.

### Naming Variables
Every time we make a variable we also have to give it a name so that we can access it later on. We call this binding a value to a name. 
- Names can be any word including digits but names cannot start with a digit.
- A name may include a dollar sign or an underscore but not other punctuation.
- We cannot have spaces in our variable names
- If we have a long name with multiple words in it we capitalise the start of each new word to make it easier to read. This is called camel case.
- There are some special [***reserved words***](https://www.w3schools.com/js/js_reserved.asp) in Javascript that we cannot use as variable names.

*It is said that the hardest thing in programming is naming variables!*

***

# Types
Javascript is what is called a weakly typed language, this means that when we create a variable we do not have to define what the type is. It also gives us the concept of automatic type conversion, which means Javascript automatically picks the type for us and will change and adapt it due to circumstance. 

```js
console.log(90 * null)
// → 0
console.log("6" - 2)
// → 4
console.log("8" + 1)
// → 81
console.log("six" * 8)
// → NaN
console.log(false == 0)
// → true
```
