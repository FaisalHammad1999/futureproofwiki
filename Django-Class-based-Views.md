## Dogs
```python
# adoption/views.py

from django.views.generic import ListView

...

class DoggosList(ListView):

    model = Dog
    context_object_name = 'dogs'
```
***
```python
# adoption/urls.py

urlpatterns = [
    path('', views.DoggosList.as_view(), name='adoption-home'),
...
```
***
`adoption/home.html` = `adoption/dog_list.html`
***
## About
```python
from django.views.generic import TemplateView, ListView

...

class AboutView(TemplateView):
    template_name = 'adoption/about.html'
```
```python
from .views import DoggosList, AboutView, create, show

urlpatterns = [
    path('', DoggosList.as_view(), name='adoption-home'),
    path('about/', AboutView.as_view(), name='adoption-about'),
```
***
## New
```python
from django.views.generic import View, TemplateView, ListView

from .forms import NewDogForm, AdoptDogForm
...

class NewDogFormView(View):
    form_class = NewDogForm
    initial = {'key': 'value'}
    template_name = 'dogs/new.html'

    def get(self, request):
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {'form': form})

    def post(self, request):
        form = self.form_class(request.POST)
        if form.is_valid():
            dog_id = form.save().id
            return HttpResponseRedirect(f'/dogs/{dog_id}')

        return render(request, self.template_name, {'form': form})
```
```python
from django.urls import path
from .views import DoggosList, AboutView, NewDogFormView

urlpatterns = [
    path('', DoggosList.as_view(), name='adoption-home'),
    path('about/', AboutView.as_view(), name='adoption-about'),
    path('dogs/new/', NewDogFormView.as_view(), name='dog-create'),
```
***
## Detail
```python
class DogDetailView(View):
    model = Dog
    form_class = AdoptDogForm
    template_name = 'dogs/show.html'

    def get(self, request, dog_id):
        dog = get_object_or_404(Dog, pk=dog_id)
        form = self.form_class(initial={'owner': request.user})
        return render(request, self.template_name, {'dog': dog, 'form': form})

    def post(self, request, dog_id):
        dog = get_object_or_404(Dog, pk=dog_id)
        form = self.form_class(request.POST)
        if form.is_valid():
            dog.owner = request.user
            dog.save()
            return HttpResponseRedirect(f'/dogs/{dog_id}')
        return render(request, self.template_name, {'dog': dog, 'form': form})
```
```python
from django.urls import path
from .views import DoggosList, AboutView, NewDogFormView, DogDetailView

urlpatterns = [
    path('', DoggosList.as_view(), name='adoption-home'),
    path('about/', AboutView.as_view(), name='adoption-about'),
    path('dogs/new/', NewDogFormView.as_view(), name='dog-create'),
    path('dogs/<int:dog_id>/', DogDetailView.as_view() , name='dog-show')
]
```