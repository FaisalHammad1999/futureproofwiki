_Just enough JavaScript to get going!_

***

## Data Types
### Primitives
| name        | description      |
| ------------- |:-------------:|
string | `“Hello world”`
number | `1`
boolean | `true / false`
null | `null` - represents intentional absence of value
undefined | `undefined` - value assigned to newly declared variables
symbol | `Symbol(‘Earth’)` - used to make a unique identifier. This is a newer data type. 
bigint | `10n` - used to reliably represent whole numbers larger than 2^53 - 1 

### Complex
| name        | description      |
| ------------- |:-------------:|
array | `[“Zelda”, “Tigerlily”, “Flora”]`
object | `{ name: “Zelda, age: 3 }`
function | `function (num) { return num + 3 }` \ `num => num + 3` - this is the ‘new’ arrow notation of ECMAScript 2015

***

## Storing Data - Variables
| name        | description      |
| ------------- |:-------------:|
var | Function or global scope. Does not have to be initialized to a value
let | Block scoped. Does not have to be initialized to a value
const | Block scoped. Must be initialized to a value. Cannot be reassigned

***

## Some Essential Operators
| operator        | description      |
| ------------- |:-------------:|
`-    /     *` | Add, subtract, divide, multiply
`**` | exponentiation - ‘to the power of’.  
`%` | modulo - returns whole number remainder of a division eg `5%3 => 2`
`=` | assignment  - assigns a value to a variable.  
`==` | equal - returns true if both sides are equal (does not check for data type)
`===` | strict equal - returns true if both sides are equal and of same data type 
`!` | logical NOT - eg. `!true => false`

***

## Comments
```js
// JS comments are two forward slashes
```

## Naming Convention
[camelCase](https://en.wikipedia.org/wiki/Camel_case)
