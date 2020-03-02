To run the slides: `./show.sh`.

# Virtualenv

```
$ python3 -m venv my_virtualenv
```

Also use `system-site-packages` if needed.

Then

```
$ source my_virtualenv/bin/activate
```

(Unix)

or

```
> \path\to\env\Scripts\activate
```

See: https://virtualenv.pypa.io/en/stable/userguide/

You're in your virtualenv.

```
$ deactivate
```

# Python Packaging

Once in a virtualenv use `pip` to install a package.

Try:

```
$ pip install requests
```

See: pypi.python.org and https://pypi.org/

* `requirements.txt` (recommend pinned version numbers)
* Source packages
* Wheels for binary packaging
* Don't use `setup.py`
* It's a mess but getting fixed

For more information see:

https://packaging.python.org/key_projects/
https://pythonwheels.com/

Common commands and use cases for `pip`:

```
$ pip install -r requirements.txt

$ pip list

$ pip freeze

$ pip install --upgrade

$ pip uninstall

$ pip search "pgzero"
```

Various pip related behaviours can be overridden and configured by the `pip`
configuration file.

Do you want to package..?

See: https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/


# Python Core / Basics

* Use Python 3
* Indentation and conventions (4 spaces, PEP8)
* Objects not variables (how naming works in Python)
* Basic types: `str`, `int`, `float`, `bool`, `byte`.
* Basic arithmetic operators.
* Using the REPL (`help` / `dir`)
* Zen of Python (`import this`)
* The community (and where to find it: https://python.org)

Learner task:

* Create a script that emits "Hello World".

I should be able to see something like this:

```
$ python hello.py
Hello, World!
```

## Control Flow:

* User input with `input`
* Conditionals (`if`, `elif`, `else`)
* Loops (`while` and `for`)
* `break`
* Exception handling (`try`, `except`, `finally`)

Learner task:

* Number guessing game (demonstrate `random.randint`)

I should see something like this:

```
$ python guess.py
Try to guess my number (between 1-100) within 1 goes.
Guess 1: 50
Too low. Try again.
Guess 2: 75
Too high. Try again.
Guess 3: 63
Correct. You win!
```

## Data Structures

* Lists - bound by square brackets: `[1, 2, "a", "b", ]`
* Tuples - think of them as immutable lists (bound by parenthesis): `(1, 2, "a", "b")`
* Sets - each item is unique: `{1, 2, 3}`
* Dictionaries - key value pairs: `{"a": 1, "b": 2}` (keys must be immutable)
* Iterables (an object capable of returning its members one at a time).
* Indexing and slicing via square brackets!

Learner task:

* In the REPL, make some lists, sets and dicts (as we go along explaining the
  above).

##Functions:

* Basic syntax
* Docstrings
* Default values for args (don't use mutables!)
* `*args` and  `**kwargs`
* `return` a result.

```
def hello(name="World!"):
    """
    Returns a friendly greeting.
    """
    return "Hello, " + name

print(hello("Nicholas"))
print(hello())
```

Learner task:

* Create a pig latin function (see example app for solution).
* How could you make it more flexible? (Default args etc)

# Modules

* Using import to get other modules
* Batteries included - popular modules for certain tasks

Zen of Python:
-------------

```
import this
```

Tools to help write idiomatic Python:

* `pycodestyle`
* `flake8`
* `pyflakes`
* `black`
* Wrap these in a `Makefile` to automate the process of code quality cheking.

# LUNCH

Lunchtime task: Make a `piglatin` module.

# Documenting Python projects with Sphinx

```
$ pip install sphinx
$ mkdir doc
$ cd doc
$ sphinx-quickstart
$ make html
```

You have basic docs..!

In order to "autodoc" from your commentary in code try this with your PigLatin
module:

Update `conf.py` to include path for code (uncomment imports and extensions
from line 13 to look like this):

```
import os
import sys
sys.path.insert(0, os.path.abspath('..'))
```

Ensure the autodoc extensions are enabled:

```
extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.viewcode'
]
```

Add:

```
.. automodule:: app
    :members:
```

To your `index.rst` then `make` again.

Markdown support: http://www.sphinx-doc.org/en/stable/markdown.html

Go visit https://readthedocs.org/ and then https://writethedocs.org/ for more
advanced help on documenting in Python.


# Testing with PyTest

```
$ pip install pytest
$ mkdir tests
$ cd tests
$ touch __init__.py
```

Add following function to `test_piglatin.py`:

```
def test_translate():
    assert True
```

Then, to run your new test try:

```
$ cd ..
$ pytest
```

Fill in some proper unit tests with assert. Further documentation at
https://pytest.org/

Mocking objects and spying on interactions via the `unittest.mock` module.

See PigLatin module for examples.

We'll cover the xUnit style `unittest` framework with the Django API work
that's coming later.

# Debugging with PDB

```
import pdb; pdb.set_trace()
```

or in Python3.7 + 

```
breakpoint()
```

Use `help` to discover the commands available to you.

* Step over lines
* Step into callables
* Step out of blocks
* Finish loops
* Evaluate objects
* Change things on-the-fly.


It's an interactive REPL for a running program.

You should probably use a visual debugger if that's what you're used to from
Java. But it's important to know you can debug scripts like this.

# DJANGO and Django Rest Framework

We're going to build a simple API for musicians and albums. See the example
code for a working example.

New virtualenv.

```
$ pip install djangorestframework
$ pip install markdown
$ pip install django-filter
```

Use Django admin commands (explain) to start a new project:

```
$ django-admin startproject myapi
$ cd myapi
$ ./manage.py migrate
$ ./manage.py createsuperuser
$ ./manage.py runserver
```

It works. Play with the `admin` application to demonstrate CRUD (add a new
user).

Add the Django Rest Framework into this "vanilla" website.

```
$ cd myapi/myapi
```

In settings.py (explain the concept of "apps" in Django):

```
INSTALLED_APPS = [
    ...
    'rest_framework',
]

REST_FRAMEWORK = {
    # Use Django's standard `django.contrib.auth` permissions,
    # or allow read-only access for unauthenticated users.
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}
```

In urls.py (explain routes, serializers, viewsets):

```
from django.conf.urls import url, include
from django.contrib.auth.models import User
from rest_framework import routers, serializers, viewsets

# Serializers define the API representation.
class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User
        fields = ['url', 'username', 'email', 'is_staff']

# ViewSets define the view behavior.
class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

# Routers provide an easy way of automatically determining the URL conf.
router = routers.DefaultRouter()
router.register(r'users', UserViewSet)

# Wire up our API using automatic URL routing.
# Additionally, we include login URLs for the browsable API.
urlpatterns = [
    url(r'^', include(router.urls)),
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```

Now browse the API in the website.

We need to add musicians and albums now we have a working API.


## Customizing things:

Create and configure a `musicians` app:

```
$ ./manage.py startapp musicians 
$ cd myapi
```

Add "musicians" to installed apps in `settings.py`.

```
$ cd ..
$ cd musicians
```

Make models that look like this in `models.py`:

```
from django.db import models

# Create your models here.

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)

class Album(models.Model):
    artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.IntegerField()
```

Explain migrations and make some for the musicians:

```
$ ./manage.py makemigrations
$ ./manage.py migrate
```

Add industry standard swagger docs to your API:

```
$ pip install -U drf-yasg
```

Update `settings.py` to have "drf_yasg" as an installed app.

Update `urls.py` to:

```
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi



... serializers, views and routes for musicians and albums...


schema_view = get_schema_view(
   openapi.Info(
      title="Snippets API",
      default_version='v1',
      description="Test description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@snippets.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=(permissions.AllowAny,),
)



   url(r'^swagger(?P<format>\.json|\.yaml)$', schema_view.without_ui(cache_timeout=0), name='schema-json'),
   url(r'^swagger/$', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
   url(r'^redoc/$', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
```

It should just work (tm):

```
$ ./manage.py runserver
```

Visit: http://127.0.0.1:8000/redoc/


## Adding Tests

In `musician/tests.py`:

```
from django.test import TestCase
from .models import Musician, Album

# Create your tests here.

class MusicianModelTests(TestCase):

    def test_create_musician(self):
        m = Musician(first_name="Nicholas", last_name="Tollervey", instrument="Tuba")
        m.save()
        result = Musician.objects.filter(first_name="Nicholas").first()
        assert result.last_name == "Tollervey"
```

This uses the familiar xUnit class based testing practice.

To run the test suite:

```
$ ./manage.py test
```

Use of client and REPL with Django to also demonstrate client based tests.

```
$ ./manage.py shell
```

This is a REPL for your web application.


```
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
>>> from django.test import Client
>>> client = Client()
>>> response = client.get('/')
>>> response.status_code
>>> dir(response)
>>> response.json()
```

Turned into a class based test:

```
from django.test import TestCase, Client


class ViewTests(TestCase):

    def setUp(self):
        """
        Run before all other tests.
        """
        self.client = Client()

    def test_root_path(self):
        result = self.client.get("/")
        assert result.status_code == 200
        data = result.json()
        assert "users" in data
        assert "musicians" in data
        assert "albums" in data
```

Then:

$ ./manage.py test

Mocking. See:

https://github.com/nhsd-ddce/dos-capacity-status-api/blob/master/application/api/capacityauth/tests/test_authorise.py

See the example app for a working API.

# Questions..?
