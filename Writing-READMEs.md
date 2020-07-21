_Please use this guide to build your READMEs on the course and beyond._

## Code of Conduct

* All work must include a README with the following elements:

    * Must have:
        * Project title
        * Project description
        * Installation & usage
        * Technologies
        * Process
        * Licence

    * Should have:
        * Screenshots/Images
        * Wins & Challenges

    * Could have:
        * Badges
        * Contribution guide
        * Code snippets
        * Bugs
        * Future features

## Useful Tools

* [Markdown Guide](https://guides.github.com/features/mastering-markdown/)
* [Badges](https://medium.com/better-programming/add-badges-to-a-github-repository-716d2988dc6a)

## Example README

Below is an example of what a README could look like.

# FizzBuzz

[![GitHub license](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)](https://github.com/Naereen/StrapDown.js/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/Naereen/StrapDown.js.svg)](https://GitHub.com/Naereen/StrapDown.js/releases/)
[![Github all releases](https://img.shields.io/github/downloads/Naereen/StrapDown.js/total.svg)](https://GitHub.com/Naereen/StrapDown.js/releases/)


![FizzBuzz Logo](https://i.imgur.com/Pi0rDBC.png)

A childrens game, now built in JavaScript to practise basic methods.

## Installation & Usage

### Installation

* Clone or download the repo.

### Usage

* Open the terminal.
* Navigate to app.js.
* Call `fizzBuzz(30)` on the command line.

## Technologies

* JavaScript

## Process

* Started by writing some pseudo code to break down the logic.
* Began by writing a function and giving a parameter.
* Implemented a loop to make the function count.
```javascript
for(let counter = 0; counter <= number; counter++){
    console.log(counter)
}
```
* Added some conditional if/else statements.
* Used `console.log()` to get output.
* Ran the function to test.

## Wins & Challenges

### Wins

* Managed to implement a loop.
* Learned how to use modulus operator.
```javascript
(counter % 3 === 0 && counter % 5 === 0)
```

### Challenges

* Had to reorder if/else statements to check for divisible by 3 & 5 first.

## Bugs

* There is no error handling so the code breaks if the arguement entered is not an integer.
* There is no limit on the argument number.

## Future Features

* HTML/CSS for a fun game.
* Deployed on Heroku.

## Licence

* [MIT licence](https://opensource.org/licenses/mit-license.php) 