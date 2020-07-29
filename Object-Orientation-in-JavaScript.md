## The Problem
Consider this code. What potential issues does it present? Is it DRY? What kind of 'things' do `zelda` and `mochi` represent?
```js
const zelda = {
    name: "Zelda",
    dob: 180726,
    owner: null,
    getAdoptionStatus: function(){ this.owner ? console.log(`${this.name} has been adopted by ${this.owner}!`) : console.log(`${this.name} is still searching for their forever home!`) },
    adopt: function(newOwner){ this.owner = newOwner }
};

const mochi = {
    name: "Mochi",
    dob: 200401,
    owner: null,
    getAdoptionStatus: () => { this.owner ? console.log(`${this.name} has been adopted by ${this.owner}!`) : console.log(`${this.name} is still searching for their forever home!`) },
    adopt: newOwner => this.owner = newOwner
};
```

As humans, we've probably ascertained that Zelda and Mochi are animals at an adoption shelter. It is natural then that they both can be `adopt`ed and we can `getAdoptionStatus` for each. JavaScript, however, has no concept of what an animal is, what is unique to each of them and what they have in common.

In this example, Zelda, Mochi and we can assume all the residents of this shelter have unique names, DOBs and arrive with no owner. They all have the ability to be adopted and to have their status checked.

Aside from JavaScript not having any concept of what an animal is this code is also repetitive. For every new arrival, I'd have to explicity define an almost identical object.

## The Solution
We are going to make our own prototype to 'teach' JavaScript what an animal is. This is nothing new - we have already used many of the built in objects such as String, Array, Date. We just need a new one: Animal.

We are going to look at 2 different syntaxes for this but it is important to remember that in JavaScript, the ES6 class syntax is purely syntactic sugar - it doesn't change how it works. In JavaScript we have prototypal inheritence, not class(ical) inheritance. The ES6 class syntax does abstract away some of the business and make it appear to be more like the classes we see in other languages such as python but it does not change the actual inner workings.

**What is `this`?** \
You're about to start seeing the keyword `this` everywhere. `this` can be determined in a few different ways but we can boil it down to the fact that in English, we need context to determine what a person means when they say 'this' and it is the same in JavaScript. More on `this` [here](https://github.com/getfutureproof/fp_guides_wiki/wiki/Call-vs-Apply-vs-Bind)!

#### ES5 Syntax
We going to make a constructor function that we run every time we want a new ***instance*** of Animal.
```js
function Animal(name, dob, owner=null) {
    this.name = name;
    this.dob = dob;
    this.owner = owner;
};
```

Now let's add some methods and calculated properties to the prototype of Animal
```js
Animal.prototype.speak = function(){ console.log(`${this.name} says hello!`) }

Animal.prototype.adopt = function(newOwner){ this.owner = newOwner; };

// defining a 'getter' property
Object.defineProperty(Animal.prototype, 'adoptionStatus', {
    get() { return this.owner ? `${this.name} has been adopted by ${this.owner}!` : `${this.name} is still searching for their forever home!` }
})
```

Once we have our constructor function we can create new instances the same way we can with any built in object.
```js
let zelda = new Animal('Zelda', 180726);
let mochi = new Animal('Mochi', 200401);
```

Now both Zelda and Mochi can have `.name`, `.dob`, `.owner`, `.adopt`, `.getAdoptionStatus` and `speak` called on them.

We were able to say 'Zelda is a new Animal with a name of 'Zelda' and DOB of 180726 and JavaScript was able to do the rest. What is very cool is that if I ask `zelda instanceof Animal` I'll get `true`! JavaScript now knows what an animal can do and that zelda is one.


#### ES6 Class Syntax
Let's do exactly the same in ES6 Class Syntax:
```js
class Animal{
    constructor(name, dob, owner=null){
        this.name = name;
        this.dob = dob;
        this.owner = owner;
    }

    speak(){ console.log(`${this.name} says hello!`); }
    
    adopt(newOwner){ this.owner = newOwner; };

    get adoptionStatus(){
        return this.owner ? `${this.name} has been adopted by ${this.owner}!` : `${this.name} is still searching for their forever home!` 
    };
};

let zelda = new Animal('Zelda', 180726);
let mochi = new Animal('Mochi', 200401);
```

## Inheritance
Okay this is great but what if we want to get more specific? Zelda and Mochi are both Animals but Zelda is a Cat and Mochi is a Dog. At the moment, we have taught JavaScript what an Animal is but not Cat or Dog. We could go about it the same way as we did above but we are at risk of getting repetitive. Cats and Dogs can both do all of the things an Animal can do - because they are still animals.

This is where inheritance can come in to help out.
#### ES5 Syntax
I'll still want to make a constructor for my new Cat class. This time we will call the Animal constructor from inside it to get access to those name, dob and owner properties. Note the passing of `this` as the first argument. That is a requirement of the `call` method - see [here](https://github.com/getfutureproof/fp_guides_wiki/wiki/Call-vs-Apply-vs-Bind) for more info.
```js
function Cat(name, dob, owner) {
    Animal.call(this, name, dob, owner);
};
```
Now we can make a new Cat:
```js
let zelda = new Cat('Zelda', 180726);

zelda.dob; //=> 180726
zelda.adoptionStatus; //=> undefined
```
and we would have access to `zelda.name`, `zelda.dob` and `zelda.owner` but we've not actually inherited anything yet. To do this we'll need to set the prototype of our Cat.
```js
Object.setPrototypeOf(Cat.prototype, Animal.prototype);

zelda.adoptionStatus; //=> 'Zelda is still looking for their forever home!'
```

Let's take it a step further with dogs by adding some custom functionality.
```js
const dogBreeds = ['shih-poo', 'labrador', 'greyhound'];

function Dog(breedIdx, name, dob, owner) { // my Dog constructor takes in more than the Animal one
    Animal.call(this, name, dob, owner); // Animal receives only what it needs
    this.breed = dogBreeds[breedIdx]; // I use the remaining for some custom constructor business
};

Dog.prototype.speak = function(){ console.log(`${this.name} says Woof!`); } // Adding a custom method
Object.setPrototypeOf(Dog.prototype, Animal.prototype); // Setting the prototype to Animal
```

#### ES6 Class Syntax
Again, the actual behind-the-scenes business here is exactly the same as above but with syntactic sugar.

```js
// Cat class has access to the Animal functionality
class Cat extends Animal {}; 
// the extends keyword is the equivalent of `Object.setPrototypeOf(Cat.prototype, Animal.prototype)`


// Dog class has access to the Animal functionality, a custom method 
// and some custom constructor functionality
const dogBreeds = ['shih-poo', 'labrador', 'greyhound'];

class Dog extends Animal {
    constructor(breedIdx, name, dob, owner){
        super(name, dob, owner); // super() is the equivalent of `Animal.call(this)
        this.breed = dogBreeds[breedIdx];
    };

    speak(){ console.log('Woof!'); } // defining our custom method
};
```

And now check out how much we've taught JavaScript!
```js
let mochi = new Dog(0, 'Mochi', 200401); // passing breed index, name and dob

mochi instanceof Animal; //=> true
mochi instanceof Cat; //=> false
mochi instanceof Dog; //=> true
```

## Custom Errors using OOP
OO is useful in all sorts of circumstances and now you know how inheritance works, let's use it to make a custom Error.
[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) is a built-in object and a great one to start using OO inheritance to customize.

```js
class RealityError extends Error {
    constructor(){
        super('Warning, reality has been breached');
        this.name = 'RealityError';
    };
};

class LOLError extends Error {
    constructor(msg){
        super(`LOOOOL! ${msg}!`);
        this.name = 'LOLError';
    };
};
```

To understand what is happening, check out the Error documentation. We'll see that a standard [Error constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/Error) can take an optional argument of a message which is what we've passed to it via `super`.

We've then accessed one of the Error [instance properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error#Instance_properties) of name.

We can also see that when we call it we will be able to access the other instance properties and the single method it comes with.

#### Using our custom error
Using our custom error works just like using any other Error. You can `throw` the Error with the intention of it being 'caught' elsewhere.

`try ... catch` is a great way to handle this. Test this out in your console:
```js
function realityCheck(pigsCanFly){
    try {
        if(pigsCanFly){
            console.log(`Well that's a surprise`);
            throw new RealityError()
        } else {
            throw new LOLError("Pigs can't fly!")
        };
    } catch (err) {
        console.error(`Oops! There's been an ${err.name}! It says... "${err.message}"`);
    };
};

realityCheck(); //=> LOLError
realityCheck(true); //=> RealityError
```