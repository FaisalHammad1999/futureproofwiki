# Testing
Testing is a fundamental part of the software development cycle. It is important to test to ensure that our system is working as expected. \
Testing happens at multiple different stages of our application development and we can structure the different types of test we do into a pyramid that signifies the different levels. \
There are several 'patterns' and 'antipatterns' to consider when approaching your tests. Check out [all these pyramids](http://www.testingreferences.com/here_be_pyramids.php)!

The og. test pyramid was a concept of [Mike Cohn](https://martinfowler.com/articles/practical-test-pyramid.html) \
![Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid/testPyramid.png "Mike Cohn's Test Pyramid")

The top benefits and take-aways from this approach are:
1. Write tests with different granularity
2. The more high-level you get the fewer tests you should have

***

### Unit Tests
Unit testing targets small, isolated chunks of code eg. a function. \
They are easy to implement, quick to run and give very specific feedback. \
The majority of your testing should be based around unit tests.

### Service Tests
Other terms you may hear for tests around this level are "Integration", "API" and more. \
These tests start to bring together units and test them as a group.

### UI Tests
Other terms you may hear for tests around this level are "System", "Behaviour" and more. \
This is where your complete program will start to be tested to find the end result.

### BONUS: Acceptance / End-to-End / Manual Tests
The highest level of testing, usually done manually.

***

## Antipatterns
Consider the ice-cream cone antipattern next to this lovely ~~volcano~~ pyramid. What issues could this bring?
![Ice-Cream vs Volcano](https://i.stack.imgur.com/uq7Gh.png)

***

# TDD
Test Driven Development is an approach where we write failing tests first and then work to get them passing. This means that you will be writing tests very early in your development process if using TDD. Testing does automatically equal TDD.

***

# Tools & Frameworks
There is a plethora of testing frameworks available for you to choose from including: \
[Mocha](https://mochajs.org/) | [Tape](https://github.com/substack/tape) | [Jasmine](https://jasmine.github.io/) | [Jest](https://jestjs.io/) and many more...

Check out our walkthroughs for setting up JS Unit tests with two options:
- [Jest](https://github.com/getfutureproof/fp_guides_wiki/wiki/JS-Unit-Testing-with-Jest)
- [Mocha & Chai](https://github.com/getfutureproof/fp_guides_wiki/wiki/JS-Unit-Testing-with-Mocha-and-Chai)



***
