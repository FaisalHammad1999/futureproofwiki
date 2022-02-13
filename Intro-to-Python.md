## What is Python?

Python is a programming language first created by Guido van Rossum between 1989 and 1991.

It can be applied in may use-cases including building web applications, data analysis, data visualisation and machine learning to name a few. It is used not only by Software Engineers but also Mathematicians and Data Analysts amongst others.

Python is popular as it can be used to solve complex problems but has a beginner friendly, easy-to-learn syntax. Due to this it has a large community as well as a substantial ecosystem of libraries, frameworks and tools. It's also open source, aka free.

## Who uses it?

Many of the biggest tech companies in the world reply heavily on Python as one of their key languages.

Netflix for example, uses Python for its machine learning capabilities, to recommend films and tv that you may enjoy based on past preferences. Dropbox is another, which has used Python to scale rapidly, even hiring its creator Guido van Rossum to work with them for a while. Other notable users includes Google and Spotify. [Paypal](https://github.com/paypal/Checkout-Python-SDK) and [NASA](https://github.com/nasa/apod-api) also use Python, have a look at their APIs.

A more comprehensive list of the companies that use Python can be found [here](https://stackshare.io/python).

## Getting Started

_Python is dead. Long live Python!_

Your machine (looking at you Mac users) may have Python 2 already installed, however as of 1st January 2020, Python 2 is deprecated and no longer supported by the Python Software Foundation. 

⚠️ **We are going to be using Python 3.** ⚠️

Make sure you have this installed. Use `$ python --version` in your terminal to check your current version.

To get started we can simply type `$ python` (Mac) or `$ py` (Windows) into the command line to open up the Python interpreter. We can use this to run commands - try `print("Hello world")`.

When using your IDE make sure that your project is actually running on the version of python 3 that you actually need (i.e. Python 3.10). 

Python 3.10 has added several new features that won't run in previous versions or they might even raise errors. With Visual Studio Code you can know the version looking at the bottom left of your IDE window. If it's not the version you want / need, click there and look for it. 

If it's not there, go to the command pallete and type "Python Select Interpreter". Then look for the version you need. 

## Variables

We already know about variables from JavaScript and they function in much the same way in Python with a few differences.

A variable in Python should follow this syntax: `variable_name = variable_value`.

Note that we are no longer using 🐪 `camelCase` but 🐍 `snake_case` instead. This is one of the many `rules` of writing pythonic code. Python developers are know for their strict adherence to the PEP8 Style Guide which you can view [here](https://www.python.org/dev/peps/pep-0008/#a-foolish-consistency-is-the-hobgoblin-of-little-minds). Alternatively check out [this](https://realpython.com/python-pep8/) condensed version. 

Some of the most important rules when writing variables in Python include:
* A variable's name can be made up of letters, numbers or underscores.
* A variable's name cannot begin with a number, only a letter or underscore.
* A variable's name cannot be one of the reserved words in Python, for example True, False or None. _Full list [here](https://www.programiz.com/python-programming/keyword-list)_

## Data Types

In Python we can use **Integers, Floats, Strings and Booleans** just like JavaScript. However there are some new data types to be aware of.

### Lists 

Like a JavaScript array these contain comma separated lists of values which can be accessed by their index. Lists are **mutable**, i.e. they can be altered after creation by adding or removing items from the list.

```python
shopping_list = ['bread', 'milk', 'sugar']
```

### Tuples

Similar to a list these also contain comma separated values accessed by their index, however it is important to note that these are **immutable** - they cannot be changed after they are created.

```python
seasons_of_the_year = ('Winter', 'Spring', 'Summer', 'Autumn')
```

### Dictionaries

These are reminiscent of JavaScript objects, key-value pairs. They are unordered and referenced by their key. Dictionaries are **mutable**.

```python
my_ship = {
    'name': 'USS Enterprise',
    'registry': 'NCC-1701-D',
    'class': 'Galaxy'
}
```

### Sets

A set is a collection of values which is unordered and unindexed. Set items are unique and while a set itself may be modified (you can add or remove), the elements contained in the set must be of an immutable type. They cannot be accessed by reference to the element or index, but you can loop through them to see if a value is present.

```python
to_do_list = {'Eat', 'Sleep', 'Wash', 'Repeat'}
```

## String Formatting

### %-formatting

This is no longer recommended as your code can become unreadable as you increase the amount of parameters. There have been updates to Python since which make formatting strings easier, but it's good to know incase you come across old code which contains %-formatting.

```python
>>> name = 'Sabrina'

>>> occupation = 'Teenage Witch'

>>> 'My name is %s and I am a %s.' % (name, occupation)

# My name is Sabrina and I am a Teenage Witch.
```

### str.format()

Again, avoid using this method as it is outdated and has been replaced. It's is much more readable than code using %-formatting, but can be quite verbose.

```python
>>> name = 'Salem'

>>> occupation = 'Cat'

>>> 'My name is {} and I am a {}.'.format(name, occupation)

# My name is Salem and I am a Cat.
```

or

```python
>>> person = {'name': 'Hilda', 'occupation': 'Sabrina\'s Aunt'}

>>> 'My name is {name} and I am {occupation}.'.format(name=person['name'], occupation=person['occupation'])

# My name is Hilda and I am Sabrina's Aunt
```

### f-Strings

New to Python 3, this is the way to go. The expressions are evaluated at runtime and then formatted, making them much more versatile and way less verbose.

```python
>>> name = 'Zelda'

>>> occupation = 'Sabrina\'s Other Aunt'

>>> f'My name is {name} and I am {occupation}.'

# My name is Zelda and I am Sabrina's Other Aunt
```

As f-strings are evaluated at runtime you can also use them to perform actions, functions or methods

```python
>>> f'This potions calls for {3 * 5} eye of newt'

# This potions calls for 15 eye of newt
```

or 

```python
>>> def point_finger():
        return 'Casting spell'

>>> f'{point_finger()} at you.'

# Casting spell at you.
```

Check out further examples [here](https://realpython.com/python-f-strings/)

## Functions

### Built-in

Python has a range of built in functions available to use, you can find the full list [here](https://docs.python.org/3/library/functions.html#len).

### Writing your own

We can also create functions of our own.

The basic syntax of a function should look like:

```python
def function_name (arguments):
     # write code here
     return return_value
```

Take note of the  indentation. These indents are how Python knows where a specific block of code starts and ends. This enables python to be more readable as we don’t have all the additional punctuation we have in other languages. Python is very strict with indentation and will get confused if you mix tabs and spaces! Just be consistent and you will be okay! Tools are available to automatically 'translate' one to the other if you want to make sure! Technically 4 spaces is the official python indentation.

### Basic function

```python
def add_5(number):
    return number + 5
```

### Multiple parameters

We can pass in multiple parameters to our functions.

```python
def buying_confirmation(item_name, number):
    sentence = 'You are buying ' + number + ' ' + item_name + '(s)'
    return sentence
```

### Default values

We can give our parameters names and default values. When you call a function with keyword arguments you can pass in the arguments in any order.

```python
def shape_volume(height = 1, width = 1, depth = 1):
    shape_volume = height * width * depth
    return shape_volume
```

Try to:

- Run `shape_volume()` (no arguments)
- Run `shape_volume(3, 4, 5)` (arguments without their keywords)
- Run `shape_volume(depth = 3, height = 6, width = 10)` (changed order)
- Run `shape_volume(width = 9, height = 4)` (missing argument)


We can store the result of calling a function as a new variable to be reused

```python
volume = shape_volume()
```

### Return Values

We can return multiple items by separating them with a comma.

```python
def shape_measurements(height = 1, width = 1, depth = 1):
    shape_volume = height * width * depth
    shape_area = height * width
    return shape_volume, shape_area
```

We can also store these in a variable but need to match the number of variables we are creating to the number returned by the function. The below is not best practice:

```python
volume = shape_measurements(4, 5, 6)
```

Instead we can deconstruct:

```python
volume, area = shape_measurements(3, 5, 90)
```

## Scope

A local variable, that is, a variable written inside of a function for example, can only used inside of that function.

A global variable is a variable defined outside of a function and can be used anywhere.

If we are in local scope and we want a variable to be available globally, we can use the `global` keyword to do so:

```python
def make_ice_cream:
    global flavour_of_the_day
    flavour_of_the_day = 'Mint Chocolate Chip'

make_ice_cream()

flavour_of_the_day

# 'Mint Chocolate Chip'
```

## Modules
To use a module (built-in, installed or custom), use the `import` statement:
```python
import math

factorialOf6 = math.factorial(6)
```

You can bring in named imports
```python
from math import factorial

factorialOf6 = factorial(6)
```

Or rename an imported library
```python
import math as number_things

factorialOf6 = number_things.factorial(6)
```

PEP8 advises to use absolute paths as a preferred option although this is not always viable. For more on imports, check out [this guide](https://realpython.com/absolute-vs-relative-python-imports/) 