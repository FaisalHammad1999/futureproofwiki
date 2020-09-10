If you haven't already - meet [pip](https://pypi.org/project/pip/), Python's package management system.

Pip is great and easy to use but one issue is that it will install packages globally on your machine rather than locally per project as npm does. This is an issue when you are building projects. You can use Python without creating a virtual environment but this is not recommended and could cause you a host of problems further down the line.

> Say for example you have built a Python project in the past that used the latest version of Django, which at the time was 2.2.16. You want to build a new project with Django using the latest version, now 3.1.1. You have a choice. You can `pip install` the latest version of Django, potentially making your previous project unusable on your machine in the process, or you can not upgrade but miss out on all of the shiny new features of Django 3.1.1. It also means it may be difficult for other people to use your code as who knows what their setup is like.

**Thankfully there is a solution - use a virtual environment!**

Play it safe. Always use one.

## Pipenv

As ever there are many options out there when it comes to managing your dependencies correctly. Below we will cover one of the most popular, [pipenv](https://pypi.org/project/pipenv/), but feel free to make your own choice.

Pipenv is a dependency manager which aims to bridge the gap between packages like [venv](https://docs.python.org/3/library/venv.html) and pip, which is why it is so widely used.

### Install

Contrary to the above warning, it is acceptable to install some basic building blocks for a Python workflow such as pipenv.

**To install:** `$ pip install pipenv`

**To upgrade:** `$ pip install --upgrade pipenv`

### Getting Started

To create a virtual environment on your project, simply `cd` into the root folder and run: `$ pipenv shell`.

Your command line should look something like `(project_name) -> project_name`. Don't worry if it doesn't, it will very per machine and OS.

You will also notice that a `Pipfile` has appeared in your repository. This is where pipenv will track the packages you use for the project.

If you want to leave the virtual environment at any time you can use: `$ exit`

### Using Pipenv

We can now install packages via pipenv. Say I wanted to install Flask, I'd use: `$ pipenv install flask.

It's also possible to uninstall using: `$ pipenv uninstall flask`.

Now you will see flask has been added to the `Pipfile` and a new `Pipfile.lock` has also appeared. This lets pipenv know which specific version of each package was used when building the app to avoiding the risks of automatically upgrading packages and breaking the project.

> Remember to launch the shell if you have exited the command line and relaunch. Your project may not run if you are not in the shell.