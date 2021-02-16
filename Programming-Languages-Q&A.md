## The Million Dollar Question
**Q**: Which languages should I learn?

**A**: It doesn't really matter! Concepts over syntax!

There are hundreds (in the region of 700 according to [Wikipedia](https://en.wikipedia.org/wiki/List_of_programming_languages)) of programming languages out there! Learning a specific language is fun and depending on your work/school/project, you may be focusing on a specific one at any given time but the skills you are learning whilst doing that will apply to hundreds more! The thing to focus on when learning any language is the **concepts**, not the syntax. Whatever language you are using, the trinity of Data, Logic and Actions will always be in play. For example, knowing what the concept of [iteration](https://www.google.com/search?q=define%20iteration%20programming) is, will help you in any language. Knowing the syntax for iterating in [LOLCODE](http://www.lolcode.org/) off the top of your head will probably only help you in LOLCODE. As long as you know the concept, you can always Google 'iteration in LOLCODE' or search the docs.

## A Short History of Programming Languages
Computers, at their heart, have not changed. They monitor voltage switches from 0v to 5v and based on those combinations of signals, do different things. A bit like Morse code _(see [this interesting post](https://cs.stackexchange.com/questions/39920/is-morse-code-binary-ternary-or-quinary) to consider the similarities and differences)_! At the outset, engineers had to actually compose binary code and soon Assembly (asm) languages were created in which engineers could write line by line instructions which were then 'assembled' into the binary machine code. These assembly languages are still very 'low-level' and not much easier than simply writing the machine code.

As time went on, more and more versions of these languages were created at increasingly higher levels of abstraction and these days, we have many languages which require no mathematical notations, come bundled with lots of useful extras to make our lives easier, and feel almost like writing in plain English.

But it all comes down to 0s and 1s in the end.

***

## Useful Definitions
It is worth having a basic understanding of the most common definitions that you may come across when categorising languages:

***

### Compiled vs Interpreted
**Q**: Okay, so if computers only understand 0s and 1s? How will they know how to run my code?

**A**: Absolutely! Regardless of how you wrote your code, it will at some point need to be turned into binary. A compiled language needs to be, well, compiled, before running. The compilation stage will produce a binary translation of your code that any computer can run. An interpreted language can use an interpreter program (yep, that's a program to run your program) which will do the 'translation' in realtime as the code is run.

There is a great example of compiled vs interpreted languages using hummus available [here, courtesy of FreeCodeCamp](https://www.freecodecamp.org/news/compiled-versus-interpreted-languages/).

***

### Weakly vs Strongly Typed
**Q**: What is 2 + "2"?

**A**: Well, it could be 22, 4, or just a big old error. If you are working with a weakly typed language, it will do its best to implicitly convert types in order to achieve its goal. This can be very useful in some cases and a disaster in others. If you are working with a strongly typed language, your code will flat out refuse to even try to convert a number into a string or vice versa without explicit instruction.

_Note that 'typed' does not refer to keyboard typing here, but data types!_

### Statically vs Dynamically Typed
As the name suggests, this is also related to data types. It refers to the moment in time that it looks at all the data and makes sure that it is of the expected type. Statically typed languages check for this during compilation. Dynamically typed languages check at runtime.

A good description of Weakly/Strongly/Dynamically/Statically Typed is available [here, courtesy of edspresso](https://www.educative.io/edpresso/statically-v-dynamically-v-strongly-v-weakly-typed-languages)

***

### Paradigms: Functional vs Object Oriented vs Procedural
**Q**: What is a paradigm?

**A**: Essentially a paradigm is just a model or an approach. Three of the common programming paradigms are 'functional', 'object oriented' (OOP) and 'procedural'

- **OOP** is an approach where we can create and access Objects that can model real world items or services eg. Cat or Order.

- **Procedural** programming makes use of 'subroutines' (functions!) to group together a set of instructions to produce a result. These subroutines can be stored and used at any time. In procedural programming, our functions can update the state of a program eg. change a variable.

- **Functional** programming is similar in look to procedural programming but the functions created cannot update the state of a program eg. change variables.

_Note: many languages allow you to work in more than one paradigm. Often your use case is more likely to be the decider when choosing a paradigm._
