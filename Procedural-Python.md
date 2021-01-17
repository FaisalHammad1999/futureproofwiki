Most of the concepts we are looking at here are already familiar, we are merely translating them into a different language. Some languages have more built in methods than others and some excel in different areas but remember that logic is logic and that is why the universal language of pseudocode can be so helpful!

## Control Flow
### `if` statements
A go-to structure for control flow in most languages is some kind of 'if' statement. The bones of a Python if statement looks like this:
```python
if condition:
    do_this
elif conditon:
    do_that
else:
    do_the_other
```

Note how it is very recognisable but with some syntax differences. In Python, we have an `elif` instead of JavaScript's `else if` or Ruby's `elsif`. When switching languages, don't worry if you get these things mixed up - it comes with time and also syntax highlighting!
Note alse the `:` after the condition line, we'll be writing a lot of colons `:` in Python!

### Conditions
To make our if statement useful, we need some conditions. Mathematical operators are naturally very useful here. Let's make a Python FizzBuzz:
```python
num = 86

if num % 5 == 0 and num % 3 == 0:
    print('FizzBuzz')
elif num % 5 == 0:
    print('Fizz')
elif num % 3 == 0:
    print('Buzz')
else:
    print(num)
```

*Note the `and` operator and the double equals `==` equality operator.*

***

### Find item(s) in collection
Let's say we have a list of items in our fridge:
```python
fridge = ['hummus', 'celery', 'cucumber', 'cucumber', 'beetroot']
```

I want to know if I have hummus in the fridge. The `in` operator can help.
```python
if 'hummus' in fridge:
    print('We have hummus!')
```

Now we can see why it is said Python is very readable! To negate this (see if there is not hummus in the fridge)..
```python
if 'hummus' not in fridge:
    print('We are out of hummus!!!')
```

But what if I want to know how many of an item I have?
```python
cucCount = fridge.count('cucumber')
print(f'We have {cucCount} cucumbers!')
```

### Ternary operator (conditional expression)
Python does have a ternary structure which is suitable for a quick condition check that does not require a multiline `if` statement.
```python
print('Yay!') if 'hummus' in fridge else print('Get ye to the shop.')
```

***

## Loops
### for loop
We've seen for and while loops in JavaScript. Let's have a look in Python:

I want to print out every item in my fridge:
```python
for item in fridge:
    print(item)
```

### while loop
A while loop, as we've seen before, will run the its block as long as the condition is truthy. What is this code doing?
```python
from time import sleep

timer = 10
print('Loading')

while timer > 0:
    print('.', end=' ', flush=True)
    sleep(1)
    timer -= 1

print('Complete!')
```

***

## Iteration: built-ins, lambdas & list comprehension, oh my!
### Built-in methods
*For a full list of Python's many built-in methods, check out the [documentation](https://www.w3schools.com/python/python_ref_functions.asp). We'll look at just two of them here.*

Often we need to create a new collection based on an existing one. We have previously used `map` and `filter` for this and Python also has `map` and `filter` which receive two arguments: a function to which each item in the collection is yielded and the collection itself.

```python
fridge = ['hummus', 'celery', 'cucumber', 'cucumber', 'beetroot']

def firstLetter(item):
    return item[0]

initials = map(firstLetter, fridge)
```

```python
def beginsWithC(item):
    return item[0] == 'c'

aisle_c = filter(beginsWithC, fridge)
```

### Lambdas
In the above examples, we are passing a named function as the first argument. Whilst this makes sense when the function may be multiline or re-used in other parts of the program, sometimes we just want an anonymous function here. Another name for anonymous functions is 'lambda' function. In Python, a lamba function is structured like this:
```python
# lambda <args> : <expression>
lambda item : item[0]
```
The return value of the expression is implicitly returned so we do not use the `return` keyword here. Let's rewrite our map and filter examples above to use lambdas:

```python
fridge = ['hummus', 'celery', 'cucumber', 'cucumber', 'beetroot']

initials = map(lambda item : item[0], fridge)

aisle_c = filter(lambda item : item[0] == 'c', fridge)
```

### List comprehension
Whilst these lambdas are great, they may be starting to get a bit less human-readable. Another option we have specifically when needing to create new lists from existing lists, is list comprehension. Let's refactor our work above to use list comprehensions:

```python
fridge = ['hummus', 'celery', 'cucumber', 'cucumber', 'beetroot']

initials = [item[0] for item in fridge]

aisle_c = [item for item in fridge if item[0] == 'c']
```

***

## Modules
In the timer example above, there was a line at the top, `from time import sleep` - what was that?! 

This is the syntax used to import Python [**modules**](https://www.w3schools.com/python/python_modules.asp), whether they are ones that are built-in, ones we have installed, or modules we have created ourselves.

To import the complete module:
- `import <mod-name>` : import the whole module
- `time.<method1>()` : access individual methods via the mod name

To import specific methods from a module:
- `from <mod-name> import <method1>, <method2>` : import specific methods
- `method1()` : call imported methods directly

### Built-in modules
[`time`](https://docs.python.org/3/library/time.html#module-time) is an example of a built-in module, we did not have to install it separately or build it ourselves. The full list of built-in modules is available [here](https://docs.python.org/3/py-modindex.html). To use them, simply `import <module-name>` at the top of your `.py` file.

### Installable packages & modules
Many other people have created some great packages for us to install and use in our own code. Packages are essentially a collection of `.py` modules. Once you have installed a package (we recommend using pipenv and pip to manage your project's packages), you can import the package's modules in the same way as a built in module.
- `pipenv shell` : not required but recommended for package management (see [guide](https://github.com/getfutureproof/fp_guides_wiki/wiki/Virtual-Environment) for details)
- `pip install <package>` : install package
- `import <package>` : import entire package
- `from <package> import <module>` : import specific module from package
- `from <package> import <module>.<method>` : import specific method from package

### Build-your-own
A module is in essence just a `.py` file! Here we are making an animal_translator module and importing its `translate` function into `app.py`.

```python
# in animal_transalator.py
noises = {
    'sheep': 'baa',
    'dog': 'woof',
    'cat': 'miau'
}

def translate(animal, phrase):
    translation = " ".join([noises[animal] for word in phrase.split(' ')])
    return translation.capitalize()
```

```python
# in app.py
from animal_translator import translate

phrase = 'Hello, field'
translate('sheep', phrase)
```

***For further details on taking your modules and creating packages, check out [this article](https://python-packaging-tutorial.readthedocs.io/en/latest/setup_py.html).***

***

## Debugging
There are several options for debugging tools in Python, the in-built [`pdb`](https://docs.python.org/3/library/pdb.html) module is a great place to start!

Import the pdb module and then anywhere you want a breakpoint, add `pdb.set_trace()`
```python
import pdb

def say_my_name(name):
    pdb.set_trace()
    greeting = f'What\'s my name? {name.upper()}!'
    print(greeting)

say_my_name('Priyanka')
```

On running the above code, we will get 'paused' in our terminal within a pdb prompt. We have stopped time! In the pdb prompt, type `name` and hit enter. We see 'Priyanka'. Now try `greeting`. We get a NameError! But not to worry, this is just because we have paused time before 'greeting' is evaluated. To move to the next line, type `n` for next and hit enter. Now we can access `greeting` as well as `name`.

***There is plenty more to dive into with pdb and various alternative tools so do check out the [pdb documentation](https://docs.python.org/3/library/pdb.html) and research other options.***

***

## Exception handling
Things go wrong! No matter the reason, we want to get in the habit of catching errors and handling them eloquently. This avoids leaving our user in the dark when things don't go to plan and also can make our own debugging process quicker. The `try/except` structure in Python is reminiscent of what we have used in JavaScript. Consider the following code:

```python
animals = ['cat', 'dog', 'elephant']

def play():
    user_input = input("What animal are you looking for?\n (Type 'exit' to close.)\n")
    exit if user_input == 'exit' else where_is(user_input)

def where_is(ani):
    try:
        idx = animals.index(ani)
        print(f'{ani} is at index {idx}')
    except ValueError:
        print(f'{ani} is not in this list, please try another animal!')
    finally:
        play()

play()
```
What would happen without a try/except when entering 'caterpillar'? Under what cirumstances will the `finally` run?

***Learn more about try/except [here](https://www.w3schools.com/python/python_try_except.asp) and for a deeper dive into Python errors and exceptions, check out the [documentation](https://docs.python.org/3/tutorial/errors.html).***

***

## Comments
A comment in Python can be made with the `#` block comment.
```python
# greeting text tbd
greeting = 'good day'
```

For comments designed to make a more general description we can use a [documentational string](https://www.python.org/dev/peps/pep-0257/#:~:text=A%20docstring%20is%20a%20string,module%20should%20also%20have%20docstrings.). The PEP8 guide suggests that we should *"Write docstrings for all public modules, functions, classes, and methods."*
```python
def fizzbuzz(num):
    ''' Takes an integer and returns its fizzbuzz value '''
    if num % 5 == 0 and num % 3 == 0:
        # ...etc
```

***


## Resources
[W3Schools Python Operators](https://www.w3schools.com/python/python_operators.asp) \
[W3Schools Python built-in methods](https://www.w3schools.com/python/python_ref_functions.asp) \
[The Joy of Packaging](https://python-packaging-tutorial.readthedocs.io/en/latest/setup_py.html) \
[Debugging with pdb](https://docs.python.org/3/library/pdb.html)