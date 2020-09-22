* `pipenv install djangorestframework`
* add to installed apps
* remove templates from shelter
* remove forms from shelter
* change main urls (comment out auth for now)
```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/dogs/', include('adoption.urls')),
    # path('signup/', user_views.signup, name='signup'),
    # path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    # path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout')
]
```
* create `shelter/serializers.py`
```
from rest_framework import serializers
from .models import Dog

class DogSerializer(serializers.ModelSerializer):

    class Meta:
        model = Dog
        fields = ('id', 'name', 'breed', 'owner')
```
* shelter/views.py
* remove 
```
from django.contrib.auth.decorators import login_required
from .forms import NewDogForm, AdoptDogForm
```
* add
```
from rest_framework.views import APIView
from rest_framework.response import Response
...
from .serializers import DogSerializer
```
* add first view
```
class DogList(APIView):

    def get(self, _request):
        dogs = Dog.objects.all()
        serializer = DogSerializer(dogs, many=True)

        return Response(serializer.data)
```
* change shelter/urls
```
from django.urls import path
from .views import DogList

urlpatterns = [
    path('', DogList.as_view()),
    # path('about/', views.about, name='adoption-about'),
    # path('dogs/new/', views.create, name='dog-create'),
    # path('dogs/<int:dog_id>/', views.show, name='dog-show')
]
```
* runserver, go to http://127.0.0.1:8000/api/dogs/
***