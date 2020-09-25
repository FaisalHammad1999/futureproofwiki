We started out by building our Django app using Function-based Views with good reason! This was originally the only way to build in Django and is still incredibly useful to know. However, as Django grew, Class-based Views were introduced as a way to speed up development. Their usefulness is at it's peak when writing tried and tested functionality built in many applications. Class-based views are a potentially more elegant way of writing a Django app, condensing boilerplate code into pre-built classes that can be reused and extended.

This tutorial will cover some of the most common uses, building upon what we learned in the [Django tutorial](https://github.com/getfutureproof/fp_guides_wiki/wiki/Django).

Take a look at the official documentation for a better understanding of the full extent and power of Django's [Class-based Views](https://docs.djangoproject.com/en/3.1/topics/class-based-views/). 💻

## Template View

This is one of the easiest views to implement and it does a lot of the heavy lifting for us.

```python
# adoption/views.py

from django.views.generic import TemplateView, ListView

...

class AboutView(TemplateView):
    template_name = 'adoption/about.html'
```

As you can see below we are extending the `TemplateView` to create one of our own, passing it the name of the template we want to display. This will look for a a template folder, and find a match.

Then we simply need to update our URLs, passing the class instead of the function.

```python
# adoption/urls.py

from .views import AboutView

urlpatterns = [
    path('', AboutView.as_view(), name='adoption-about'),
```
***
## List View
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
from .views import DoggosList, AboutView

urlpatterns = [
    path('', views.DoggosList.as_view(), name='adoption-home'),
    path('about/', AboutView.as_view(), name='adoption-about'),
...
```
***
`adoption/home.html` = `adoption/dog_list.html`
***
## Form View
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
## Detail View
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
## Register View
Login and Logout can stay the same.
```python
from django.views.generic import View

class UserSignupFormView(View):
    form_class = UserSignupForm
    initial = {'key': 'value'}
    template_name = 'users/signup.html'

    def get(self, request):
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {'form': form})

    def post(self, request):
        form = self.form_class(request.POST)
        if form.is_valid():
            form.save()
            return redirect('login')
        return render(request, 'users/signup.html', {'form': form})
```
```python
...
from users.views import UserSignupFormView
from django.contrib.auth import views as auth_views

urlpatterns = [
...
    path('signup/', UserSignupFormView.as_view(), name='signup'),
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout')
```
***
## Protected View
```python
from django.utils.decorators import method_decorator
from django.contrib.auth.decorators import login_required
...
class NewDogFormView(View):
    form_class = NewDogForm
    initial = {'key': 'value'}
    template_name = 'dogs/new.html'

    @method_decorator(login_required)
    def dispatch(self, request, *args, **kwargs):
        return super(NewDogFormView, self).dispatch(request, *args, **kwargs)

    def get(self, request):
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {'form': form})

    def post(self, request):
        form = self.form_class(request.POST)
        if form.is_valid():
            dog_id = form.save().id
            return HttpResponseRedirect(f'/dogs/{dog_id}')

        return render(request, self.template_name, {'form': form})
...
```