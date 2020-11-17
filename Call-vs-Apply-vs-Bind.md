If you are smart about your context handling, you may not find yourself using these methods too often but they can be extremely useful when needing to change or lock in context. Bear in mind that context cannot always be changed. For example, arrow functions always look to the parent scope to find `this`. This can be very useful at times, and not at others! Keep an eye out and if you are having problems with `this`, take a moment to consider your context.

```js
// Demo data
class Animal {
    constructor(name){
        this.name = name
    }

    todayIAte(bfast, lunch, dinner){
        return `I am ${this.name}! Today I ate ${bfast}, ${lunch} and ${dinner}!`
    }
}

const zelda = new Animal("Zelda")
const mochi = new Animal("Mochi")

// -------------------------------

function todayIDid(activity){
     return `I am ${this.name}! Today I did ${activity}!`
}

const todayIDidArrow = activity => `I am ${this.name}! Today I did ${activity}!`
```

## .call & .apply
Call and Apply are very similar in that they immediately invoke the function or method they are called on. The difference is in how arguments are passed in.
### Call
`.call` takes any number of arguments - the context, followed by any arguments, passed individually
```js
zelda.todayIAte('gravy chunks', 'chicken', 'kibble') //=> "I am Zelda! Today I ate gravy chunks, chicken and kibble!"
zelda.todayIAte.call(mochi, 'gravy chunks', 'chicken', 'kibble') //=> "I am Mochi! Today I ate gravy chunks, chicken and kibble!"

todayIDid('chasing things') //=> "I am undefined! Today I did chasing things!"
todayIDid.call(zelda, 'chasing things') //=> "I am Zelda! Today I did chasing things!"

todayIDidArrow('chasing things') //=> "I am undefined! Today I did chasing things!"
todayIDidArrow.call(zelda, 'chasing things') //=> "I am undefined! Today I did chasing things!"
```

### Apply
`.apply` receives two arguments - the context and an array of arguments
```js
zelda.todayIAte('gravy chunks', 'chicken', 'kibble') //=> "I am Zelda! Today I ate gravy chunks, chicken and kibble!"
zelda.todayIAte.apply(mochi, ['gravy chunks', 'chicken', 'kibble']) //=> "I am Mochi! Today I ate gravy chunks, chicken and kibble!"

todayIDid('chasing things') //=> "I am undefined! Today I did chasing things!"
todayIDid.apply(zelda, ['chasing things']) //=> "I am Zelda! Today I did chasing things!"

todayIDidArrow('chasing things') //=> "I am undefined! Today I did chasing things!"
todayIDidArrow.call(zelda, ['chasing things']) //=> "I am undefined! Today I did chasing things!"
```

## .bind
`.bind` does not immediately invoke the function or method it is called on but rather returns a new function that will execute with the context given as the argument to the `.bind`
```js
const mochiContext = zelda.todayIAte.bind(mochi)
mochiContext('gravy chunks', 'chicken', 'kibble') //=> "I am Mochi! Today I ate gravy chunks, chicken and kibble!"

const zeldaDid = todayIDid.bind(zelda)
zeldaDid('chasing things') //=> "I am Zelda! Today I did chasing things!"

const zeldaDidArrow = todayIDidArrow.bind(zelda)
zeldaDidArrow('chasing things') //=> "I am undefined! Today I did chasing things!"
```