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

    def get(self, request, format=None):
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
* insert screenshot
***
* add create route and status
```
...
from rest_framework import status
...

class DogList(APIView):
...

    def post(self, request, format=None):
        serializer = DogSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
* remove `path('dogs/new/', views.create, name='dog-create'),` from urls
* insert screenshot
***
* add detail view get
```
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
```
* make sure you have `from django.http import Http40`
* change url
```
from django.urls import path
from .views import DogList, DogDetail

urlpatterns = [
    path('', DogList.as_view()),
    path('<int:dog_id>/', DogDetail.as_view())
    # path('about/', views.about, name='adoption-about'),
    
]
```
* remove 
```
def not_found_404(request, exception):
    data = { 'err': exception }
    return render(request, 'adoption/404.html', data)
```
&&
```
# shelter/urls.py
handler404 = 'adoption.views.not_found_404'
```
* add put and delete
```
class DogDetail(APIView):

...

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