We have already started working with objects in Python. What do you anticipate this will tell us? `type("Hello")`
Why does `"hello" + 5` not work? They are different types of object - a string and an integer. They have their own methods and attributes based on what object they are an instance of.

Thinking back to OO in JavaScript, we learnt that JS uses prototypal inheritance, even when using the ES6 class syntax to make things look a bit 'friendlier'. Python is one of the languages which uses class-based inheritance so when using Python classes, you might be reminded of ES6 class syntax.

## Class declaration
I wish for a new class! Use the `class` keyword:
```python
class Genie():
```

## Create new instances
```python
blue = Genie()
```

## Object initialisation
Initialisation, as we've seen in OOP before, is where we bring a new ***instance*** of an object into being. Our initialisation method will need to receive any data about this specific instance that we know from the get-go. These pieces of data will be assigned to ***attributes*** of the new instance. If no information is needed to be set, you do not have to define this method. To access the attribute later, you can use dot notation.
```python
class Genie():
    def __init__(self, name)
        self._name = name

blue = Genie('Robin')
```

## Instance methods
When calling an instance method, the instance on which it has been called is implicitly passed as the first argument. We need to take that into account when defining the method. You can add additional arguments too but you never need to explicitly pass in `self`.
```python
class Genie():
## ...etc...
    def introduce(self):
        return f'Greetings! I am a magical genie called {self._name}.'

    def grant_wish(self, wish):
        return f'The all-powerful {self._name} grants your wish to {wish}!'

# Usage
blue = Genie('Robin')
blue.introduce() #=> "Greetings! I am a magical genie called Robin."
blue.grant_wish('go on holiday') #=> "The all-powerful Robin grants your wish to go on holiday!"
```

## Class methods
Say we want to make a method that is called on the entire class of Genie, not a specific instance of Genie. For this, we use the `classmethod` [decorator](https://www.python.org/dev/peps/pep-0318/).
```python
class Genie():
    _all = ['Robin', 'Jonathan', 'Christina', 'Jeannie']

    @classmethod
    def find_by_initial(cls, initial):
        return [g for g in cls._all if g[0] == initial]

# Usage
Genie.find_by_initial('J') #=> ['Jonathan', 'Jeannie']     
```

## Static methods
Static methods are also called directly on the class but they do not receive the class itself as an argument. If you find yourself writing an instance or class method but not referencing `self` or `cls` in the body, a static method is what you need!
```python
@staticmethod
def about(cls):
    return 'Genies are awesome, in the dictionary definition of the word.'

Genie.about() #=> 'Genies are awesome, in the dictionary definition of the word.'
```

## Class attributes
If there is an attribute that you need the entire class to have access to, you can declare this within the class. It will be accessible by the class and all its instances.
```python
class Character:
    _all = []

    def _save(self):
        self._all.append(self)
```


## Private methods / attributes
As a convention, if you see something with an single underscore in front of it, this is a signal to other developers that this method or attribute is intended to be accessed locally only. This is not actually enforced by the language but it is a good way to let other humans know the intended use.
```python
def _note_to_self(self):
    return f'Keep your enemies close, {self.name}...'

def advice(self):
    print(f'Yesterday I told myself:, "{self._note_to_self()}"')
```

## Dunder methods
The more glamorous term for a dunder method is a 'magic' method. As fancy as these sound, they are simply pre-defined methods that you can add to Python classes to extend their functionality. A great one is `__str__` in which you can change how the instance is displayed as a string. By default, this will look something like `<__main__.Genie object at 0x10be5ed68`. By adding a `__str__` you can make this as approachable/sassy/silly as you like! The full list is documented [here](https://docs.python.org/3/reference/datamodel.html#special-method-names).
```python
class Genie():
    def __init__(self, name, movie):
        self._name = name
        self._movie = movie

    def __str__(self):
        return f'{self._name} the Genie from {self._movie}'
```

## Inheritance
Inheritance is very easy in Python! Simply pass the parent class as an argument to the child.
```python
class Character():
    def __init__(self, name, movie):
        self._name = name
        self._movie = movie

    def accept_award(self):
        print(f'Thank you to everyone for this award for {self._movie}.')

class Genie(Character):
    def grant_wish(self):
        print(f'The almighty {self._name} grants your wish!')

robin = Genie('Genie', 'Aladdin')
jonathan = Genie('Jafar', 'Aladdin')
nathan = Character('Timon', 'The Lion King')

jonathan.grant_wish() # "The almighty Jafar grants your wish!"
robin.accept_award() # "Thank you to everyone for this award for Aladdin."
nathan.accept_award() # "Thank you to everyone for this award for The Lion King."
```

If you want any custom initialization activity in a child class, you can call init on super.
```python
class Character():
    def __init__(self, name, movie):
        self._name = name
        self._movie = movie

class Genie(Character):
    def __init__(self, name, master):
        super().__init__(name, movie)
            self.master = master
```

## Relationships
Whilst you are likely to be using a database and ORM/ODM if handling complex data, let's have a look at how we could model a one-to-many relationship in these classes. Let's say a band has many songs and a song belongs to a band. Consider the following code:

```python
class Song():
    def __init__(self, title, band):
        self._title = title
        self.set_band(band)

    def set_band(self, band):
        self._band = band
        band.add_song(song)

class Band():
    def __init__(self, name):
        self._name = name
        self._songs = []

    def add_song(song):
        self._songs.append(song)

    def print_set_list():
        for song in self._songs:
            print song._title()

```

We are initialising new instances of Band with a `songs` attribute of an empty list. When a band is started, although they may not have any songs yet, the important thing is that they have the *potential* to have songs in the future and therefore needs somewhere to store them.

In contrast, when a Song instance is initialised, it immediately knows about its band. Since we do not want a one-sided relationship, we need to let the band know about the new song at the same time. In this example we have made a Song instance method of `set_band` which handles both sides of the relationship.