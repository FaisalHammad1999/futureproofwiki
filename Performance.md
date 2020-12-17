When making software, especially at scale, we want to consider performance. When measuring performance we can look at many different aspects including speed, resource usage, reliablility, scalability and more. The most universally important aspect is speed. A load time of over 3 seconds is researched to increase the likeliness of a user giving up and going elsewhere rise from 32% to to 90%!

## Common Bottlenecks
### Large/unoptimised images
It's important to strike a balance between using high res images to create a good user experience, but ensuring they are optimised to the size of displayed image. Don’t load an 3000x1200px image for a favicon! Be conscious of using .gif or .png files as these tend to be larger than .jpegs.
### Too much JavaScript!
Whilst we are coding we might include lots of JS and then realise that we don’t need that library or refactor that code away. It is important to regularly audit your included JS files so we are not loading unnecessary code. We also might want to consider minifying our JS (and CSS) to reduce the file size.
### Unclean code
When we first learn how to code we are keen to just get it done, but there are some elements that can seriously slow down a website, loads of comments or excess whitespace for example can add serious bloat to the size of some of our files.
Other techniques such as inline styles can also add complexity that may be unneeded.
### Server location
Although we may not consider it, where your server is located can seriously implement call times. Therefore who your hosting provider is can seriously make a difference to the speed of your website.

## Tools
There are plenty of options for performance testing tools out there. Probably the most accessible entry point for speed testing is Google's [Page Speed Insights](https://developers.google.com/speed/pagespeed/insights/). You can access this via the standalone web app or as part of [Lighthouse](https://developers.google.com/web/tools/lighthouse). Available out of the box in your Chrome dev tools (see 'Audits' tab), Lighthouse gives plenty of performance feedback to be getting started with. A full Lighthouse report gives an overall 'Performance' score which is a [calculated combination](https://web.dev/performance-scoring/) of various metrics. It is calculated according to the Lighthouse team's research on the most impactful metrics on User experience.
A full Lighthouse report will include feedback on:
- Page Speed
- Accessibility
- Best Practices
- SEO coverage (take this with a pinch of salt, but it's a nice starting point)
- Progressive Web App readiness

You will also receive some pointers on how to improve your metrics and find some suggested numbers to be aiming for!

For testing your API response times, the Network tab in dev tools is a good place to start. For more data on responses, [Postman](https://www.postman.com/) is another useful tool.

**Caching** \
Caching is storing small bits of useful and reused data in memory so that we don’t have to make repeated HTTP calls or similar to retrieve the same data. You may find that thanks to caching, your speed test results fluctuate. You always have the option to test with caching, either simulated through your test tool or by manually clearing your browser cache.

***

## Algorithm Efficiency
A key area that slows down performance particularly on the backend is poorly designed algorithms. When we measure the speed of algorithms we say that we are measuring the 'complexity'. There are two key areas we can consider when evaluating an algorithms performance: the amount of memory it requires and the speed of processing.

### Big-O notation
The mathematical measure of complexity is called Big-O notation. That's the letter 'O', not the number 0!
We can measure two forms of Big-O notation: 
- average complexity
- worst case complexity
  
These refer to the size of an algorithm's input. The size of its input will always vary and has a substantial influence over the amount of processes an algorithm will have to run. For example, thinking of GPS, if the mapping system only knew about the main roads and not small roads it would be able to find a route for us quicker (although it might not necessarily be the quickest route!)

Why is it called 'Big O'? Well, my favourite online answer for this comes courtesy of FreeCodeCamp who say *'It’s called Big O notation because you put a “big O” in front of the number of operations.'* and [MIT](https://web.mit.edu/16.070/www/lecture/big_o.pdf) explain further: *'The letter O is used because the growth rate of a function is also referred to as the order of the function'*

### Common notations
A graph visual is [available here](https://www.desmos.com/calculator/kgwiv5zizm).

**Constant:** O(1) \
No matter what n is, the same number of operations occurs.
```js
function addThreeTo(n){
    return n + 3
}
```

**Linear:** O(n) \
The number of operations increases in proportion to n
```js
function doThisNTimes(n){
    for (let i = 0; i < n; i++) {
        console.log(`${i}: ${n}!`)
    }
}
```

**Quadratic:** O(n<sup>2</sup>) \
The number of operations increases quadratically (squares). \
This is a type of polynomial algorithm (eg. O(n<sup>3</sup> / O(n<sup>4</sup>))
```js
function doThatNTimes(n){
    let finalResults = []
    for (let i = 0; i < n; i++) {
        console.log(`${i}: Processing ${n}...`)
        for (let j = 0; j < n; j++) {
            console.log(`${j}: ${n}!`)
        }
    }
    return results
}
```

**Logarithmic** O(log n) \
The number of operations increases logarithmically. \
In Big O, unless specified, the base of a log is 2. ie. if `n=10`: 1, `n=20`: 2, `n=40`: 3
```js
function logThis(n) {
    for (let i = 0; i < n; i*=2) {
        console.log(`${i}: ${n}!`);  
    }
}
```

### Search Algorithms

Consider the algorithmic complexity (number of steps taken) for the following problem:
1. Player 1 chooses a number between 1 and 10
2. Player 2 makes a guess at the number
3. Player 1 says 'higher' or 'lower'
4. Repeat steps 2 & 3 until Player guesses the correct number
   
How about if the number is between 1 and 100? 1 and 1000?

#### Linear Search
Say we have an algorithm that takes an array of letters and popped out each of those letters in uppercase. Let’s call the number of array items `n`, the complexity of this algorithm would be `O(n)` because it has to do the function of going to uppercase n times (once for each letter!)

If we apply this to the problem above, the processes taken would look something like this:
- algorithm receives `chosenNumber`, `minNumber` & `maxNumber`
- make a variable `x` equal to `minNumber`
- if `x === chosenNumber`, return `x`
- if `x` does not match the chosen number, add 1 to `x`
- if `x === maxNumber`, admit defeat, the number has not been found

What is the best and worst case scenario for number of operations here?
- Best case, the first `x` value matches `chosenNumber` so... `O(1)`!
- Worst case, we have to go through all the possible numbers so `O(maxNumber)`

### Binary Search
Say we have an algorithm that takes an array of unique names in alphabetical order eg:
`["Aleksandar", "Barbara", "Beth", "Christine", "Divna", "Ian", "Irene", "George", "Olga", "Robert", "Trifun"]`. The algorithm takes a second input of a single name and we want to know if the name is in the array. Let’s again call the number of array items `n`, but this time we will not start looking at the beginning. We will instead find the mid-point of the collection (divide `n` by 2 - round up if `n` is odd to get the mid-point) and check the element at that location. If the element matches our search criteria, we're done. If not, we need to ask 'are we too high or too low?'. From that simple question, we can discard half the array and repeat the process with a smaller collection. The worst case complexity of this algorithm is `O(log n)` with a base of 2 (binary logarithm) - the power to which the number 2 must be raised to obtain the value n, ie. the number of times we need to divide by 2 to narrow down to one option.

If we apply this to the problem above, the processes taken would look something like this:
- algorithm receives 'chosenNumber', 'minNumber' & 'maxNumber'
- make variable `x` equal to mid point between 'minNumber' & 'maxNumber'
- if `x === chosenNumber`, return `x`
- if `x < chosenNumber`, set `maxNumber` to `x - 1`
- if `x > chosenNumber`, set `minNumber` to` x + 1`
- if `minNumber === maxNumber`, admit defeat, the number has not been found

What are the best and worst case scenarios for number of operations here?
- Best case, the first `x` value matches `chosenNumber` so... `O(1)`!
- Worst case, we have to go through all the possible numbers so `O(log maxNumber)`

### The dangers of nested loops
Consider this function:
```js
function allPossiblePairings(students){
    const possibles = [];
    for (firstStudent in students) {
        for (secondStudent in students){
            if (firstStudent !== secondStudent){
                possibles.push([firstStudent, secondStudent])
            }
        }
    }
};

const students = ["Bob", "Midna", "Alex", "Darryl", "Lexie"];
```
- How many operations have to happen for each item in the input array?
- How would we simplify that to a common Big O formula?
- What if we had an additional nested loop? 
- How scalable is this?
