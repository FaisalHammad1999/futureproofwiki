## Step One
```python
# adoption/views.py

from django.views.generic.list import ListView

...

class DoggosList(ListView):

    model = Dog
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