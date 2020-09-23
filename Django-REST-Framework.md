So far you've learned how to make a Django app entirely in Python using templating to display your data on HTML pages with Django's template language. This is a great way to build an app but what if we want to make this project into an API, which can be called upon for data to be displayed in a different front-end such as React.js? 

📣 Introducing [Django REST framework](https://www.django-rest-framework.org/). 📣

Please do take a look at the documentation to familiarise yourself with all the amazing features, but read on for a quick start guide to turn the Shelter Project into an easily accessible API.

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

Learn more about [Django Class Based Views]().

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

from .views import DogList, DogDetail

urlpatterns = [
    path('', DogList.as_view()),
    path('<int:dog_id>/', DogDetail.as_view())
]
```

Once again let's check if it works, this time at `http://127.0.0.1:8000/api/dogs/16`

![DogDetail](https://i.imgur.com/2o2FDTZ.png)

***
* Add urlpatterns
```
from rest_framework.urlpatterns import format_suffix_patterns

urlpatterns = [
...
]

urlpatterns = format_suffix_patterns(urlpatterns)
```
***
* Add authentication
* Delete templates in users
* Add django urls
```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/dogs/', include('adoption.urls')),
    path('api/auth/', include('rest_framework.urls'))
    # path('signup/', user_views.signup, name='signup'),
    # path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    # path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout')
]
```
* Add screenshot of login
* Protect views
```
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