If you haven't already - meet [pip](https://pypi.org/project/pip/), Python's package management system.

Pip is great and easy to use but one issue is that it will install packages globally on your machine rather than locally per project as npm does. This is an issue when you are building projects. 

> Say for example you have built a Python project in the past that used the latest version of Django, which at the time was 2.2.16. You want to build a new project with Django using the latest version, now 3.1.1. You have a choice. You can `pip install` the latest version of Django, potentially making your previous project unusable on your machine in the process, or you can not upgrade but miss out on all of the shiny new features of Django 3.1.1. It also means it may be difficult for other people to use your code as who knows what their setup is like.

**Thankfully there is a solution - use a virtual environment!**

You can use Python without creating a virtual environment but this is not recommended and could cause you a host of problems further down the line. Play it safe. Always use one.