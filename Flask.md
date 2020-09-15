If you would like a lightweight backend framework like Express but would prefer to work in Python rather than Node, you have a great option in [Flask](https://palletsprojects.com/p/flask/). It is an extremely popular option for web application backends as you can get going with very little but it can be extended as far as you need. In this walkthrough we will setup a simple API. For ways to extend your application to include eg. SSR, authentication etc. see the resources links below and most importantly, search for them! A good place to start is [this flask extensions article](https://flask.palletsprojects.com/en/1.1.x/extensions/).

## Installation
- `pip install flask` (replace pip with your installer of choice if appropriate)

## Creating a server
Lets make a file for our server to live eg. `server.py`
- Import flask with `from flask import Flask`
- Create the server with `server = Flask(__name__)`
- Write the command that will run the server `server.run()` (add argument of `debug=True` for auto restart on file changes)

## Starting your server
Now you have a file, run it from your terminal with `python server.py`
- Visit the given port (usually 5000 by default with flask)
- Note that 'the requested URL was not found on the server'
- So the server is running, but it does not have any routes

## Adding Routes
To add a basic, GET route, start with the `route` decorator called on your server (this will be the variable you set to `Flask(__name)`). Pass the route decorator a string of the path you are writing a rule for.
Directly underneath this you can write the method that will run when this endpoint is requested.
```python
@sever.route('/') # @<var-you-set-to-Flask(__name__)>
def home(): # this will run when a GET request to '/' is made
    return 'Hello from Flask!' # this plain-text will be sent in the response
```

## CORS config
For basic CORS configuration, use the [flask_cors](https://pypi.org/project/Flask-Cors/1.10.3/) library and wrap your server before running it. This will enable cross origin access on all routes, from any origin. To get more specific, check the [documentation](https://flask-cors.readthedocs.io/en/latest/)!
- `pip install flask-cors`
- `from flask_cors import CORS`
- `CORS(server)`

## Handling JSON
To send a JSON response, you just need to import `jsonify` from the `flask` library and pass it your data.
- `from flask import jsonify`
- `return jsonify(data)`
```python
all_cats = [
    {'id': 1, 'name': 'Ziggy'},
    {'id': 2, 'name': 'Zelda'}
]

@server.route('/api/cats')
def cats():
    return jsonify({'cats': all_cats})
```

To extract a JSON body from a request, you can use `request.get_json()`. Another option, if you want to make sure the headers of the incoming request declare a content-type of application/json, is `request.json`. Import `request` from flask and you're good to go.

```python
from flask import request # ...etc

# in eg. a POST route
new_cat_data = request.get_json()
```

### HTTP Verbs
Of course, we're likely to want to handle more than just GET requests. We can pass an additional argument to the `route` decorator where we state which HTTP verbs this rule should handle.
```python
@sever.route('/cats', methods=['POST']) 
def create_cat(): # this will run only when a POST request to '/cats' is made
    return 'Have some cats' # this plain-text will be sent in the response
```

We are likely to expect `/cats` to respond to both GET and POST requests. You can add as many verbs as you wish to the methods list and in the method, check the request.method to decide what to do next!
```python
from flask import request # ...etc

@server.route('/api/cats', methods=['GET', 'POST'])
def cats():
    if request.method == 'GET':
        return jsonify({'cats': all_cats})
    if request.method == 'POST':
        new_cat = request.get_json()
        all_cats.append(new_cat)
        new_cat['id'] = len(all_cats)
        return jsonify({'cat': new_cat})
```

The if statement approach is common in this setup but you could also consider a dictionary lookup:
```python
def all(r):
    return {'cats': all_cats}

def create(r):
    new_cat = r.get_json()
    all_cats.append(new_cat)
    new_cat['id'] = len(all_cats)
    return new_cat

@server.route('/api/cats', methods=['GET', 'POST'])
def cats():
    fns = {
        'GET': all,
        'POST': create
    }
    resp = fns[request.method](request)
    return jsonify(resp)
```

## Route params
You can declare route parameters in your path inside `<>` with optional typing (eg. `int:`, `string:`). They are passed as named arguments to the handler function.
```python
@server.route('/api/cats/<int:cat_id>')
def cat(cat_id):
    cat = all_cats[cat_id-1]
    return jsonify(cat)
```
***

## Query params
Query parameters are available in a dictionary via `request.args`:

In a request to:
`http://localhost:8000/cats/name_generator?first_name=Beth&birth_month=January&birth_date=8`

`request.args` would point to:
`{'first_name': 'Beth', 'birth_month'='January', 'birth_date': '8'}`


***

## SSR & Templating
In this example we have only been returning JSON data but you can, of course return almost anything, including HTML. There are certain benefits of server side rendering, especially around SEO.

To dynamically create HTML content, Flask gives us instant access to the [Jinja](https://flask.palletsprojects.com/en/1.1.x/templating/) templating engine, although there are plenty of other options. You can even use do [SSR React](https://medium.com/swlh/server-side-rendering-ssr-with-react-and-flask-47e589e1051f), as long as you re-`hydrate` your React app on the client side!

You can take this a lot further but this is a basic templating example:
```python
# in server file
from flask import request, render_template # ...etc
from dragon import name_generator

@server.route('/name_generator')
def get_name():
    dragon_name = name_generator(request.args)
    return render_template('result.html', title=f'Hello {dragon_name}!', result=dragon_name) 
```

```html
<!-- in templates/result.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{title}}</title>
</head>
<body>
    <h2>Your dragon name is...</h2>
    <h1>{{result}}</h1>
</body>
</html>
```

***

## Extensions
There are many [options](https://flask.palletsprojects.com/en/1.1.x/extensions/) to extend the functionality of your Flask app. Search [PyPi](https://pypi.org/search/?c=Framework+%3A%3A+Flask) to discover more! Here are just a few:
- Database connections
  - [Flask PyMongo](https://flask-pymongo.readthedocs.io/en/latest/) for MongoDB databases
  - [Flask SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/quickstart/) for SQL databases

- SMTP (sending emails)
  - [Flask Mailman](https://pypi.org/project/Flask-Mailman/)

- Authentication
  - [Flask Login](https://flask-login.readthedocs.io/en/latest/)
  - [Authlib](https://authlib.org/)

***



## Resources
[Official Flask Documentation](https://flask.palletsprojects.com/en/1.1.x/) \
[Mega Tutorial](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) \
[PyPi](https://pypi.org/search/?c=Framework+%3A%3A+Flask) \
[Jinja Templating](https://jinja.palletsprojects.com/en/2.11.x/) \
[React SSR with Flask](https://medium.com/swlh/server-side-rendering-ssr-with-react-and-flask-47e589e1051f) \
[Testing with pytest-flask](https://pytest-flask.readthedocs.io/en/latest/tutorial.html)