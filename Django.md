One of the benefits of using a web framework is that it 'automatically' handles a lot of the setup and many of the aspects of web development that we often find ourselves recreating every single time. For futher discussion on web frameworks, focusing on Python solutions, check out [this article](https://www.fullstackpython.com/web-frameworks.html).

We already have experience using a JavaScript backend framework, now let's have a look at a Python option called [Django](https://www.djangoproject.com/). Note the advertised 'selling points' of 'Ridiculously Fast', 'Reassuringly Secure' and 'Exceedingly Scalable' - sounds good!

One of the benefits and downfalls of Django is that it really provides a lot out of the box. If you need a full CRUD application with ready-to-go security, database configuration and more, Django could be ideal. For smaller projects, this is likely to be way more than is needed and result in unecessarily cumbersome processes and codebase.

Let's put it to the test. How quick can we put together a CRUD application in Django?

## Start a Project
### Install
- `pip install Django`
- You can confirm your version with `python -m django --version`
### Bootstrap a Django project
- `django-admin startproject <project-name>` eg. `django-admin startproject shelter`
- (To see a list of all django-admin commands, just enter `django-admin`)
### Start dev server
- `python manage.py runserver`
- Visit in browser (default port is 8000)

So in just a few commands you've got a fairly attractive visual running on your localhost development server but it's not exactly a webapp! This is merely the environment in which you will craft your work - the overall configuration. Have a look at what it gave us.
- `manage.py` sets us up to run terminal commands to interact with our project
- `__init__.py` blank but lets the system know that this is a Python project
- `settings.py` what it says on the tin! The settings for the project
- `url.py` for mapping out routes to specific sets of handlers
- `wsgi.py` some basic setup for [WSGI](https://www.python.org/dev/peps/pep-3333/) deployment
- `asgi.py` some basic setup for [ASGI](https://asgi.readthedocs.io/en/latest/introduction.html) deployment

## Make your first App
Within one Django 'project', you can use one or more 'apps'. You can think of these like microservices - they should be self contained but can work together. Let's make one called 'adoption'.
- `python manage.py startapp adoption`
  
You now have a folder called 'adoption' with a substantial amount of new files.

Finally, install your app into your project by pointing to the config class that is defined in `adoption/apps`
```python
# shelter/settings.py
INSTALLED_APPS = [
    # <appname>.apps.<AppConfigClass>
    'adoptions.apps.AdoptionConfig',
    # etc... 
]
```

***

### Add a couple of 'view's
We'll start with a very simple HTML view - just headers for an index and show route.
```python
# adoption/views.py
from django.http import HttpResponse

def index(req):
    return HttpResponse("<h1>Hello! Here are all the friends who need adopting!<h1>")

def show(req, id):
    return HttpResponse(f'<h3>Dog number {id}!</h3>')
```

To return more complex HTML, see [Templates](#templates) below

***

### Make a path to get to adoption app
Back in our overall config urls file, route all the /adoption/ routes to the adoption app:
```python
# shelter/urls.py
from django.contrib import admin # this is in by default
from django.urls import path, include # importing include as well as path

urlpatterns = [
    path('adoption/', include('adoption.urls')), # add adoption urls
    path('admin/', admin.site.urls), # this is in by default, visit :8000/admin to see
]
```
If you'd prefer to not have /adoption/ as part of your route, you can simply leave the path as an empty string.

### Map out the adoption routes
Once we get to the adoption app, we can be more specific:
```python
# adoption/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='adoption-index'), # route for /adoption/
    path('about/', views.about, name='adoption-about'), # route for /adoption/about
    # path('<id>/', views.show, name='adoption-show'), # route for /adoption/:id
    path('<int:id>/', views.show, name='adoption-show') # to accept only numbers as id param
]
```

### Try it out
Start your server as before with `python manage.py runserver`, visit `localhost:8000/adoption` and behold your index message. Visit `localhost:8000/adoptions/3` to see the show view.

***

## Database setup
*You may have noticed by now that, on running your dev server, you receive warnings about 'unapplied migrations'. Django has some default installed apps which, if used, will even provide relevant database tables. If there are any you don't want, just comment them out in the `INSTALLED_APPS` list in `<project>/settings.py`.*

Django comes with its own [ORM](https://docs.djangoproject.com/en/3.1/topics/db/queries/) which will be our interface to the database right from step one.

### Creating Models
We will start by making some models. These will end up as database tables. Let's start with a Dog and a Breed model with the idea that a Dog has a Breed. For a rundown of all the possible fieldtypes and their validations, see [this exhausive list](https://docs.djangoproject.com/en/3.1/ref/models/fields/).

```python
# adoption/models.py
from django.db import models
class Breed(models.Model):
    name = models.CharField(max_length=100)
    temperament = models.TextField()

class Dog(models.Model):
    name = models.CharField(max_length=50)
    age = models.PositiveIntegerField()
    breed = models.ForeignKey(Breed, on_delete=models.SET_NULL)
```

Ready for some magic?
- Run `python manage.py makemigrations adoption` and look in your `<app-name>/migrations` file. 
- Run those migrations with `python manage.py migrate`.

*If you'd like to see the SQL that will be run by your migrations run `python manage.py sqlmigrate <app-name> <migration #>` eg. `python manage.py sqlmigrate adoption 0001`*

Any time you wish to update your models, simply update in `models.py` then run the `makemigrations` and `migrate` commands again.

### Interacting with models
You can test these out in an interactive shell by running `python manage.py shell` and importing models as required eg. `from adoption.models import Breed`.
#### **C**reate
- `gh = Breed(name='Greyhound', temperament='kind, gentle')` make new instance
- `gh.save()` persist new instance as db record
- `gh.dog_set.create(name='Zozo', age=4)` make a new Dog record with a breed of greyhound

#### **R**ead
- `Dog.objects.all()` see all records
- `Dog.objects.first()` see first record
- `Dog.objects.get(pk=2)` get record by id
- `Dog.objects.filter(name='Mochi')` find all dogs with name 'Mochi'
- `Dog.objects.filter(breed_name='greyhound')` find all dogs with breed of name 'greyhound'
  
#### **U**pdate
- `b = Breed.objects.get(pk=2)` get breed with id 2
- `b.temperament='extra snuggly'` update temperament field
- `b.save()` save changes

#### **D**estroy
- `b.delete()`


Now you can start using these in your views:
```python
def show(req, id):
    dog = Dog.objects.get(pk=id)
    return HttpResponse(f'<h3>Meet {dog.name}!</h3>')
```
  
***There are more query methods at your disposal, for a definitive list, check the [documentation](https://docs.djangoproject.com/en/3.1/ref/models/querysets/).***

***

## Extras
### Templating
If you'd like to return more complex HTML from your routes we can use templates. Django naturally has it's own templating system. To get started, make a `templates` folder in the relevant app and create an inner folder that matches the app name:
- shelter/
  - adoptions/
    - templates/
      - adoptions/
        - index.html
        - show.html
  - shelter/
  - manage.py

And in your action, `render` the template and pass down any data required in a dictionary as the third argument to `render`.
```python
# adoption/views.py
def index(req):
    context = { 'doggos': Dog.objects.all() }
    return render(req, 'adoption/index.html', context)

def about(req):
    return render(req, 'adoption/about.html')
```

If you passed down any data, you can render it (and indeed, execute any python code) using Django's templating syntax:
```html
<!-- adoption/templates/adoption/index.html -->
<h2>Here are all the friends in need of new homes!</h2>
<ul>
    {% for dog in doggos %} <!-- {% %} to add python logic -->
        <li>{{ dog.name }}</li> <!-- {{ }} to access data -->
    {% endfor %}
</ul>
```

***For a deeper dive into templating in Django, including inheritance to share snippets between views, check out the [documentation](https://docs.djangoproject.com/en/3.1/ref/templates/language/).***

***

### Admin
Often website owners require some backend access but don't wish to touch code directly. The concept, therefore, of an Admin page is not unfamiliar. Django gives us an entire setup avaiable right out of the box with the `admin` app. It allows users to log in to view and interact with the site's data.

To make use of the admin panel (comes in a default Django setup at `/admin`), you will need to create a super user.

- `python manage.py createsuperuser` (If this throws an error, make sure you have the `django.contrib.admin` app installed in your project (see settings.py) and run the migrations).
- Follow the terminal prompts to create a new user.
- Visit `/admin` and log in with your new credentials.

To add a model to your admin, register it in your `<app-name>/admin.py` file:
```python
# adoption/admin.py
from django.contrib import admin
from .models import Dog

admin.site.register(Dog)
```

Hint: if you'd like this to list as something more 'readable' than 'Dog Object (1)', in your Dog model, add a method:
```python
# adoption/models in Dog model
    def __str__(self):
        return f'{self.name} the {self.breed__name}'
```

***For more details on usage of the admin app see the [documentation](https://docs.djangoproject.com/en/3.1/ref/contrib/admin/)***

***

### Switching databases
Django comes with a SQLite integration ready to go but you can change your database engine in `<project>/settings.py`. As we have spent some time in PostgreSQL, let's set that up in Django too. Additional documentation on this setup is available [here](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES). Your ORM methods and setup remains the same regardless of the database - handy!

Setup a local 'shelter' postgres database in your psql terminal or interface of your choice then connect it up:

```python
# shelter/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql', # default is sqlite3
        'NAME': 'shelter', # default is BASE_DIR / 'db.sqlite3'
        'USER': 'postgres', # update accordingly if appropriate
        'PORT': 5432, # 5432 is postgres default
        'HOST': 'localhost', # update accordingly if not running db locally
        'PASSWORD': None, # update accordingly if password protected
    }
}
```
If, on running your server, you receive an error about psychopg2, run `pip install psycopg2-binary`.

***

### User registration & authentication
Django has some very useful tools for User authentication. Here's a quick-start...
- `python manage.py startapp users` (and install in settings.py)

#### Registration
- make a form which extends the freebie UserCreationForm and adds any additional fields
```python
# users/forms.py
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm

class UserSignupForm(UserCreationForm):
    email = forms.EmailField()
    first_name = forms.CharField(max_length=50)
    last_name = forms.CharField(max_length=50)

    class Meta:
        model = User
        fields = ['username', 'email', 'first_name', 'last_name', 'password1', 'password2']
```
- create `register` view in `users/views.py`
- import our form `from .forms import UserSignupForm`
- render a template, passing in our form
    
```python
# users/views.py
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import UserSignupFormm

def register(req):
    if req.method == 'POST':
        form = UserSignupFormm(req.POST)
        if form.is_valid():
            form.save()
            username = form.cleaned_data.get('username')
            messages.success(req, f'Welcome, {username}!')
            return redirect('adoption-index')
    else:
        form = UserSignupFormm()
    data = {'form': form}
    return render(req, 'users/signup.html', data)
```

```html
<!-- users/templates/users/signup.html -->
    <form method="POST">
        {% csrf_token %} <!-- Django forms will not function without this additional security token-->
        {{ form.as_p }} <!-- .as_p is optional for formatting purposes -->
        <input type="submit" value="Sign Up">
    </form>
```

*NB: To see flash messages, you'll need to add them in a base template:*
```python
# a base template
    {% if messages %}
        {% for message in messages %}
            <section id="alert">{{ message }}</section>
        {% endfor %}
    {% endif %}
```

- add signup route to project urls
```python
# <project-name>/urls.py
from users import views as user_views

urlpatterns = [
    path('signup/', user_views.signup, name='signup'),
    # ...etc...
]
```

That's it! Log in to your admin console or in your sql database run `SELECT * FROM auth_users` to check! When accessing the User model via the Django ORM, you can import the model: `from django.contrib.auth.models import User` and then access as per usual.

***For more info on Django forms check out the [documentation](https://docs.djangoproject.com/en/3.1/topics/auth/).***

***
#### Authentication
- add Django's freebie login and logout views to project urls
```python
# <project-name>/urls.py
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout')
    # ...etc...
]
```

- add templates
```html
<!-- users/templates/users/login.html -->
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit" value="Login">
    </form>

    <p>Not yet a member? Sign up <a href="{% url 'register' %}">here</a></p>

<!-- users/templates/users/logout.html -->
{% block content %}
    <p>Bye! Come by again anytime <a href="{% url 'login' %}">here</a></p>
{% endblock content %}
```

- add login redirect rule
```python
# <project-name>/settings.py
LOGIN_REDIRECT_URL = 'adoption-index'
```

### Conditional rendering based on authentication status
You may wish to eg. show 'Login' or 'Logout' depending on if a User is currently authenticated. We can use the provided `user` object and check the `is_authenticated` status.
```html
<!-- base template -->
    <nav>
        {% if user.is_authenticated %}
            <a href="{% url 'logout' %}">Logout</a>
        {% else %}
            <a href="{% url 'login' %}">Login</a>
            <a href="{% url 'signup' %}">Signup</a>
        {% endif %}
    </nav>
```

### Protecting routes
You may have routes that you only wish to be accessible to logged-in users.
- add login route rule
```python
LOGIN_URL = 'login'
```

- add login_required [decorator](https://wiki.python.org/moin/PythonDecorators#What_is_a_Decorator) to any views you wish to protect
```python
# users/views.py
from django.contrib.auth.decorators import login_required

@login_required
def profile(req):
    return HttpResponse(f'<h1>{user.username}\'s profile</h1>')
```

***For more info on Django authentication check out the [documentation](https://docs.djangoproject.com/en/3.1/topics/forms/#forms-in-django).***

***

### Custom Error Routing
Django provides a very basic Not Found page for when a user tries to access a non-existing route. In order to see what this looks like in production, make the following changes in settings.py, but change debug back to True after as it's very useful in development!
```python
DEBUG = False

ALLOWED_HOSTS = ['*']
```
Visit `/bob` and see the rather uninspiring 404 page. To make a custom one:

- Create views:
```python
# adoption/views.py
def not_found_404(req, exception):
    return render(req, 'adoption/404.html')

def server_error_500(req):
    return render(req, 'adoption/500.html')
```

- Create templates:
```html
<!-- adoption/templates/adoption/404.html -->
{% extends 'adoption/base.html' %}

{% block content %}
    <h1>Oops, what are you doing out here?</h1>
    <p>Let's go <a href="{ url 'adoption-index'}">Home</a></p>
{% endblock content %}

<!-- adoption/templates/adoption/500.html -->
{% extends 'adoption/base.html' %}

{% block content %}
    <h1>It's not you, it's us</h1>
    <p>Let's go <a href="{ url 'adoption-index'}">Home</a></p>
{% endblock content %}
```

- Create handlers:
```python
# <project-name>/settings.py
handler404 = 'adoption.views.not_found_404'
handler500 = 'adoption.views.server_error_500'
```

- Enjoy!

***For more info on Django exceptions check out the [documentation](https://docs.djangoproject.com/en/3.1/ref/exceptions/).***

***

## Documentation & Tutorials
- [Official Django Quickstart](https://www.djangoproject.com/start/)
- [MDN Django Tutorial](https://docs.djangoproject.com/en/3.1/topics/db/queries/)
- [Django & React Walkthrough](https://www.valentinog.com/blog/drf/)
- [Testing in Django](https://docs.djangoproject.com/en/3.1/topics/testing/overview/)
