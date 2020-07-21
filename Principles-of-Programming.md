### Pseudocode

One of the easiest ways to plan out our logic before we start coding is to break the problem down into high-level, human readable code. \
This is called Pseudocode and you should definitely make use of it! \
Think about the logic for making calculating a tip - how would we break it down?

### Logic
The logic is the thinking part of the code, often represented by a boolean expression.
```JS
// Was the service good?
// Was the service average?
// Was the service poor?
```

### Data
The data represents the facts and/or statistics that we can input and get the program to output.
```JS
// The quality of the service.
// The value of the bill.
// The percentage to tip based on the quality of service.
```

### Action
The actions are what they say on the tin, where the code does something.
```JS
// Ask user for input on level of service.
// Calculate a percentage of the bill.
// Add the tip to the bill.
// Display the total amount owed.
```

### Testing
Testing is a very important best practice when writing code, something you should always be doing. \
You can manually test your code as you write it to see if the code is working but this can get impractical when working with large projects, so we can automate it. \
Don't worry, there are lots of frameworks out there to help us with this such as [Mocha](https://mochajs.org/) and [Jasmine](https://jasmine.github.io/). \
We will cover testing in more depth throughout the course and dive into the world of TDD (Test Driven Development).

### Debugging
This is one of the most common exercises in development. You'll likely spend a lot of your time as a developer debugging code, whether your own or others. \
Learn to love it and don't be afraid of error messages, these are your friends and guide you on your debugging journey. Stay calm, solve the error in front of you and repeat until the code works!

### Validation
Validating data is a great way to ensure your code does not unintentially break or direct users to give you the information you are looking for. \
Always assume users are out to break your code, build validation around that and you won't go wrong. \
Remember to be specific, programming languages can do clever things but you need to tell them what to do.

---

# Resources
[Art of Code - Why you should write more Pseudo Code](https://medium.com/@yonatandoron/why-you-should-write-more-pseudo-code-a3a27bcffbd4)