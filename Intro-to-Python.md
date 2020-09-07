## What is Python?

Python is a high-level, object-oriented programming language created by Guido van Rossum between 1989 and 1991.

It can be used for building web applications, data analysis, data visualisation and machine learning to name a few. It is used not only by Software Engineers but also Mathematicians and Data Analysts amongst others.

Python is popular as it can be used to solve complex problems but has a beginner friendly, easy-to-learn syntax. Due to this it has a large community as well as a substantial ecosystem of libraries, frameworks and tools. It's also open source, aka free.

## Who uses it?

Many of the biggest tech companies in the world reply heavily on Python as one of, or their main language.

Netflix for example, uses Python for its machine learning capabilities, to recommend films and tv that you may enjoy based on past preferences. Dropbox is another, which has used Python to scale rapidly, even hiring its creator Guido van Rossum to work with them for a while. Other notable users includes Google and Spotify. [Paypal](https://github.com/paypal/Checkout-Python-SDK) and [NASA](https://github.com/nasa/apod-api) also use Python, have a look at their APIs.

A more comprehensive list of the companies that use Python can be found [here](https://stackshare.io/python).

## Getting Started

_Python is dead. Long live Python!_

Your machine (looking at you Mac users) may have Python 2 already installed, however as of 1st January 2020, Python 2 is depreciated and no longer supported by the Python Software Foundation. 

⚠️ **We are going to be using Python 3.** ⚠️

Make sure you have this installed. Use `$ python --version` to make sure you have the latest release.

To get started we can simply type `$ python` (Mac) or `$ py` (Windows) into the command line to open up the Python interpreter. We can use this to run commands - try `print("Hello world")`.

## Variables

We already know about variables from JavaScript and they function in much the same way in Python with a few differences.

A variable in Python should follow this syntax: `variable_name = variable_value`.

Note that we are no longer using 🐪 `camelCase` but 🐍 `snake_case` instead. This is one of the many `rules` of writing pythonic code. Python developers are know for their strict adherence to the PEP8 Style Guide which you can view [here](https://www.python.org/dev/peps/pep-0008/#a-foolish-consistency-is-the-hobgoblin-of-little-minds). Alternatively check out [this](https://realpython.com/python-pep8/) condensed version. 

Some of the most important rules when writing variables in Python include:
* A variable's name can be made up of letters, numbers or underscores.
* A variable's name cannot begin with a number, only a letter or underscore.
* A variable's name cannot be one of the reserved words in Python, for example True, False or None. _Full list [here](https://www.programiz.com/python-programming/keyword-list)_

## Data Types

In Python we can use **Integers, Floats, Strings and Booleans** just like JavaScript. However there are some new data types fto be aware of.

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

These are reminiscent of JavaScript objects, key-value pairs. They are unordered and referenced by their key, dictionaries are **mutable**.

```python
my_ship = {
    'name': 'USS Enterprise'
    'registry': 'NCC-1701-D'
    'class': 'Galaxy'
}
```

