We have many options when in comes to testing our Python applications and [`pytest`](https://docs.pytest.org/en/stable/)is one of the most popular choices thanks to its user-friendly API and flexibility to slide into a wide variety of situations. Another very popular option is Python's built-in [`unittest`](https://docs.python.org/3/library/unittest.html) module.

## Installation
More than likely we only need our test runner in a development enviroment so we can install it as a dev dependency where possible. In pipenv, use the `--dev` flag:
- `pipenv install --dev pytest`
  
## Creating test files
By default, pytest will look through your codebase for files named `test_<something>.py`. They can be at different levels or you can keep them in one place, as long as the file naming convention is maintained, pytest will find it. You can change this with [custom config](https://docs.pytest.org/en/stable/customize.html) if you need to. Usually your test file name would mirror the name of the file or item you are testing.
- `stuff.py`
- `test_stuff.py`

## Creating test functions
Within those test files, pytest will look for functions named `test_<something>`. Again, you can change this in a [custom config](https://docs.pytest.org/en/stable/customize.html) if you so desire.

```python
# test_stuff.py
def test_sheep_translator():
```

## Basic assertion
One of the lovely benefits of `pytest` over some other options, including python's built-in [`unittest`](https://docs.python.org/3/library/unittest.html) module, is the extra intelligent functionality it adds to the regular `assert` to give more useful insight on when something doesn't go to plan.

With `assert` we have access to operators such as `==`, `>`, `in` and more.
```python
# stuff.py
def do_maths(x, y):
    return x + y

def add_fruit(fruit):
    salad = ['banana', 'apple']
    salad.append(fruit)
    return salad
```

```python
# test_stuff.py
import stuff

def test_do_maths():
    assert stuff.do_maths(4, 2) == 6

def test_add_fruit():
    salad = stuff.add_fruit('mango')
    assert 'mango' in salad
```

## Testing for errors
Sometimes we are checking to see how things are handled if we do something that breaks our code.
```python
# stuff.py
class StuffError(Exception):
    pass

def how_many_sweets(sweets, people):
    if not people:
        raise StuffError('We need some people to share sweets with!')
    return len(sweets) / len(people)
```

```python
# test_stuff.py
import stuff
import pytest

def test_how_many_sweets():
    with pytest.raises(stuff.StuffError, match='We need some people to share sweets with!'):
        stuff.how_many_sweets(['caramel', 'humbug'], [])
```

## Group tests in classes
File and test structure is pretty flexible and can lead to difficult-to-navigate files with swathes of tests. One popular option is to use classes to group related tests together.
```python
# test_stuff.py
class TestDoMathsCase:
    def test_do_maths(self):
        assert stuff.do_maths(4, 2) == 6 

    def test_do_maths_error(self):
        with pytest.raises(stuff.StuffError, match='We can\'t add 4 and "two"...')
            stuff.do_maths(4, "two")
    
```

You can run your tests as usual, or if you want to run tests only for that test case, specify it in the command: `pytest test_stuff::TestDoMathsCase`.

## Multiple runs of one test
We may wish to run the same test with lots of different sets of input. We could put multiple `assert`s in one test function but as soon as one of those fails, you will get kicked out of the test. It would be more useful to be able to see the results of all the assertions. For this we can use pytest's [parametrize](https://docs.pytest.org/en/stable/parametrize.html).

The `@pytest.mark.parametrize` decorator takes two arguments: a string description of the values you will be providing and a list of tuples representing value sets.
```python
# test_stuff.py
@pytest.mark.parametrize('x, y, expected', [(2, 4, 6), (3, 4, 7), (9, 2, 11)])
def test_do_maths(x, y, expected):
    assert stuff.do_maths(x, y) == expected
```

## Fixtures
Fixtures are functions that are called before a test is run. They can do extra logic and/or provide extra functionality in the test. Fixtures are passed into test functions as arguments.

### Built-in fixtures
A full list of built-in pytest fixtures can be seen [here](https://docs.pytest.org/en/stable/fixture.html). Here are a couple to get you started:
#### capsys 
capsys (capture system) intercepts the stdout stream and stores it to be used in our assertions. Especially useful in a CLI application or testing server logs!
```python
# stuff.py
def greeting(name):
    print(f'Hello, {name}!')
```
```python
# test_stuff.py
def test_cli(capsys):
    stuff.greeting('Aki')
    out, err = capsys.readouterr()
    assert out == 'Hello, Aki!\n'
```

#### monkeypatch
Monkeypatch is a option for basic mocks:
```python
# test_stuff.py
def test_find_cat_by_id(monkeypatch):
    mock_cats = [{'id': 1, 'name': 'Zelda'}, {'id': 2, 'name': 'Tigerlily'}]

    monkeypatch.setattr(stuff, "cats", mock_cats)
    result = stuff.find_cat_by_id(2)
    assert result['name'] == 'Tigerlily'
```
Here we are mocking the `cats` variable so we can be certain of the data we are checking against when testing our `find_cat_by_id` function.

### Custom fixtures
We are not restricted to the built-in fixtures. You can define your own in `conftest.py`. If you like, you can have multiple `conftest.py` files at different levels across your app.

```python
# conftest.py
import pytest

@pytest.fixture
def fruits_test_data():
    return ['banana', 'apple']
```
```python
# test_stuff.py
def test_add_fruit(fruits_test_data):
    salad = stuff.add_fruit('mango', fruits_test_data)
    assert 'mango' in salad
```

## Coverage
To see pytest coverage, we will need to install [pytest-cov](https://pypi.org/project/pytest-cov/)
- `pipenv install --dev pytest-cov`
- `pytest --cov <location-to-check>`

***

## Further use and extensions
You can use pytest in the same ways above in any Python situation. There are some add-on tools which have been made for common uses in eg. Flask & Django. These usually come in the form of extra fixtures and are used in the same way as in-built and custom fixtures.

To get you going with a basic Flask API test, we can make our own custom fixture which gives us access the to `test_client` that comes with Flask.

```python
# conftest.py
import pytest
import server

@pytest.fixture
def api(monkeypatch): # Fixtures can use fixtures!
    test_dogs = [{'id': 1, 'name': 'Mochi'}, {'id': 2, 'name': 'Masha'}]
    monkeypatch.setattr(server, "dogs", test_dogs)
    # Here we are monkey patching a variable but you might well reset a test database here instead.
    api = server.app.test_client()
    return api
```

```python
# test_app.py
import json

def test_api_get_dogs(api):
    res = api.get('/api/dogs')
    assert res.json == {'dogs': [{'id': 1, 'name': 'Mochi'}, {'id': 2, 'name': 'Masha'}]}

def test_api_post_dogs(api):
    mock_data = json.dumps({'name': 'Molly'})
    mock_headers = {'Content-Type': 'application/json'}
    res = api.post('/api/dogs', data=mock_data, headers=mock_headers)
    assert res.json['dog']['id'] == 3
```

***

## Resources
[Great pytest video walkthrough](https://www.youtube.com/watch?v=fv259R38gqc) \
[pytest](https://docs.pytest.org/en/stable/) \
[pytest-flask](https://pytest-flask.readthedocs.io/en/latest/) \
[pytest-flask-sqlalchemy](https://pypi.org/project/pytest-flask-sqlalchemy/) \
[pytest-django](https://pytest-django.readthedocs.io/en/latest/)