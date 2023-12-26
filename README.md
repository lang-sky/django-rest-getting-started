# Django REST tutorial

https://www.django-rest-framework.org/tutorial/1-serialization/#tutorial-1-serialization

## Tutorial 1: Serialization

```bash
# setting up new env
python3 -m venv env
source env/bin/activate

# install packages
pip install django djangorestframework pygments

# create a new project
django-admin startproject tutorial
cd tutorial

# create an app that we'll use to create a simple Web API
python manage.py startapp snippets

# create an initial migration for our snippet model, and sync the database for the first time
python manage.py makemigrations snippets
python manage.py migrate snippets

# drop into django shell
python manage.py shell
```

In the django shell

```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework.renderers import JSONRenderer
from rest_framework.parsers import JSONParser

## create a couple of code snippets to work with
snippet = Snippet(code='foo = "bar"\n')
snippet.save()

snippet = Snippet(code='print("hello, world")\n')
snippet.save()

## take a look at serializing one of those instances
serializer = SnippetSerializer(snippet)
serializer.data
# {'id': 2, 'title': '', 'code': 'print("hello, world")\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}

## render the data into `json`.
content = JSONRenderer().render(serializer.data)
content
# b'{"id": 2, "title": "", "code": "print(\\"hello, world\\")\\n", "linenos": false, "language": "python", "style": "friendly"}'

## Deserialization
import io

stream = io.BytesIO(content)
data = JSONParser().parse(stream)

## restore those native datatypes into a fully populated object instance
serializer = SnippetSerializer(data=data)
serializer.is_valid()
# True
serializer.validated_data
# OrderedDict([('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])
serializer.save()
# <Snippet: Snippet object>

## serialize querysets instead of model instances
serializer = SnippetSerializer(Snippet.objects.all(), many=True)
serializer.data
# [OrderedDict([('id', 1), ('title', ''), ('code', 'foo = "bar"\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 2), ('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 3), ('title', ''), ('code', 'print("hello, world")'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])]

```
