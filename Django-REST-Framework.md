So far you've learned how to make a Django app entirely in Python using templating to display your data on HTML pages with Django's template language. This is a great way to build an app but what if we want to make this project into an API, which can be called upon for data to be displayed in a different front-end such as React.js? 

📣 Introducing [Django REST framework](https://www.django-rest-framework.org/). 📣

Please do take a look at the documentation to familiarise yourself with all the amazing features, but read on for a quick start guide to turn the Shelter Project from the [Django Tutorial](https://github.com/getfutureproof/fp_guides_wiki/wiki/Django) into an easily accessible API.

## Installation and Setup

Make sure you are in the root of your project folder and run: `pipenv install djangorestframework`.

You should then add it to your installed apps:

```python
#shelter/settings.py

INSTALLED_APPS = [
...
    'rest_framework',
...
]
```

_I like to add it between my custom apps and those that come with Django but feel free to add it anywhere, it doesn't matter._

Next we are going to remove files that we will no longer be needing which includes any `templates` and `forms`.

Finally let's alter our urls to reflect those of an API and remove the existing auth routes for now, which we will add back in later. 

```python
#shelter/urls.py

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/dogs/', include('adoption.urls')),
]
```

## Serializers

These help us in our pursuit of an API which can provide and receive data in a format easy to work with such as `JSON`, acting as coverters. Serializers also have the added benefit of validating data, much like a form. Read more about them [here](https://www.django-rest-framework.org/api-guide/serializers/).

Start by creating a new file `adoption/serializers.py`.

```python
# adoption/serializers.py

from rest_framework import serializers
from .models import Dog

class DogSerializer(serializers.ModelSerializer):

    class Meta:
        model = Dog
        fields = ('id', 'name', 'breed', 'owner')
```

As you can see we are extending the model serializer to make one of our own, based on the Dog model we previously created. 

The fields match these, determining what data will be passed on, adding in the `id` which is useful for dynamic displays on the front-end.

## List View

With our serializer created, we can now tell the `views.py` to use this rather than templates. To do this we are going to use Django REST framework's class based API view. 

Learn more about [Django Class Based Views](https://github.com/getfutureproof/fp_guides_wiki/wiki/Django-Class-based-Views).

```python
# adoption/views.py

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
...
from .serializers import DogSerializer

class DogList(APIView):

    def get(self, request, format=None):
        dogs = Dog.objects.all()
        serializer = DogSerializer(dogs, many=True)
        return Response(serializer.data)

    def post(self, request, format=None):
        serializer = DogSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

As you can see we are defining the `GET` and `POST` requests within the `APIView`, this replaces the need for conditionals as it is built in to the view.

For the `GET` function we still need to get all dogs, but this time pass them to the `DogSerializer` and return this instead of a template. As there will likely be more than one dog, we also need to let the serializer know that.

In the `POST` function we use the serializer to validate the data before saving, and return error status that reflect the outcome.

## URLS

The last thing we have to do in order to finish our first API route is update our urls.

```python
# adoption/urls.py

from django.urls import path
from .views import DogList

urlpatterns = [
    path('', DogList.as_view()),
]
```

Let's check that is working by running `python manage.py runserver` and navigating to `http://127.0.0.1:8000/api/dogs/`.

![DogsListGET](https://i.imgur.com/Wk5md7f.png)

We can also easily add to our dog list:

![DogListPOST](https://i.imgur.com/Sz9q3VD.png)

## Detail View

As well as seeing all of our dogs, we also may want to view individual ones. This `DetailView` is also a good place to be able to edit or delete.

It's worth checking the object exists first too and if not raising an error.

```python
# adoption/views

...
from django.http import Http40
...

class DogDetail(APIView):

    def get_object(self, dog_id):
        try:
            return Dog.objects.get(pk=dog_id)
        except Dog.DoesNotExist:
            raise Http404

    def get(self, request, dog_id, format=None):
        dog = self.get_object(dog_id)
        serializer = DogSerializer(dog)
        return Response(serializer.data)

    def put(self, request, dog_id, format=None):
        dog = self.get_object(dog_id)
        serializer = DogSerializer(dog, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, dog_id, format=None):
        dog = self.get_object(dog_id)
        dog.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

Don't forget to add this new view to the urls!

```python
# shelter/urls.py

from rest_framework.urlpatterns import format_suffix_patterns

from .views import DogList, DogDetail

urlpatterns = [
    path('', DogList.as_view()),
    path('<int:dog_id>/', DogDetail.as_view())
]

urlpatterns = format_suffix_patterns(urlpatterns)
```

At this point we can also add in the `format_suffix_patterns` method. All this does is allow users to receive the data in a format they want by adding a suffix such as `.json` to the endpoint.

Have a look at the documentation [here](https://www.django-rest-framework.org/api-guide/format-suffixes/).

Once again let's check if it works, this time at `http://127.0.0.1:8000/api/dogs/16`

![DogDetail](https://i.imgur.com/2o2FDTZ.png)

## Authentication

One of the ways in which Django can help speed up development is by giving us some frequently used functionality for free, such as authentication.

We are going to take advantage of this situation by using Django's built in auth views for Login and Logout. It's as simple as adding the below to the list of urls - you can make the endpoint anything that makes sense for your app.

```python
# shelter/urls.py

urlpatterns = [
...
    path('api/auth/', include('rest_framework.urls'))
]
```

As you will see in the top right corner we now have the ability to login.

![APIView Login](https://i.imgur.com/MrqZqxD.png)

With authentication added, it's time to protect some of our routes.

```python
# adoption/views.py

from rest_framework.permissions import BasePermission, IsAuthenticated, SAFE_METHODS
...

class ReadOnly(BasePermission):
    def has_permission(self, request, view):
        return request.method in SAFE_METHODS

class DogList(APIView):

    permission_classes = [IsAuthenticated|ReadOnly]
...

class DogDetail(APIView):

    permission_classes = [IsAuthenticated]
...
```

As you can see from the imports, we can easily stop non-logged-in users from interacting with our data thanks to Django REST framework's `IsAuthenticated` class. This is useful but we may also want to entice users to register for our site by giving an idea of some of the data we have to offer which is why it's a good idea to build a `ReadOnly` class, extending the `BasePermission`. Once that is done, we just have to let the views know which class to use and now our routes are protected.

## Registration

The last piece of the puzzle is registration. Although we can add users through the admin console, we might want people to have the ability to sign up on their own, so we have to create a register view and serializer.

```python
# users/serializers.py

from rest_framework import serializers
from django.contrib.auth.models import User

class UserRegistrationSerializer(serializers.ModelSerializer):

    password = serializers.CharField(write_only=True)
    password_confirmation = serializers.CharField(write_only=True)

    class Meta:
        model = User
        fields = ('username', 'password', 'password_confirmation')
        write_only_fields = ('password', 'password_confirmation')

    def create(self, validated_data):
        user = User.objects.create(
            username=validated_data['username']
        )
        password=validated_data['password']
        password_confirmation=validated_data['password_confirmation']

        if password != password_confirmation:
            raise serializers.ValidationError({'password': 'Passwords must match.'}) 
        user.set_password(password)
        user.save()

        return user
```

There are a few points to note here:
* We have to protect passwords by ensuring they are `write_only=True`, this includes telling the serializer to never show these fields.
* We have to use a `CREATE` rather than `POST` method when dealing with making new users, as this gives us access to the `validated_data` and `set_password` methods.
* Adding in a password_conformation is not required, but a best practice for good user experience.

Now we can update our view.

```python
# users/views.py

from rest_framework.views import APIView
from django.contrib.auth.models import User
from rest_framework.response import Response
from rest_framework import status

from .serializers import UserRegistrationSerializer


class UserRegistrationView(APIView):
    def post(self, request, format=None):
        serializer = UserRegistrationSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

This is fairly straight forward, and similar to our dog views as the serailizer is doing the hard work.

Lastly we just need to add this to our urls.

```python
# users/urls.py
from django.urls import path

from .views import UserRegistrationView

urlpatterns = [
    path('', UserRegistrationView.as_view()),
]
```
***
```python
# shelter/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/dogs/', include('adoption.urls')),
    path('api/auth/', include('rest_framework.urls')),
    path('api/register/', include('users.urls')),
]
```

And that's it - now you can add the front-end of your choice!