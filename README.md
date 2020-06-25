# myproject
This project is based on Victor Freitas Django Tutorial

![Python Api](https://github.com/jlplautz/myproject/workflows/Python%20Api/badge.svg)
[![Updates](https://pyup.io/repos/github/jlplautz/myproject/shield.svg)](https://pyup.io/repos/github/jlplautz/myproject/)
[![Python 3](https://pyup.io/repos/github/jlplautz/myproject/python-3-shield.svg)](https://pyup.io/repos/github/jlplautz/myproject/)


- Criar ambiente virtual para o projeto
  - myproject $ pipenv install django
  ```
  myproject $ ls -a
  .  ..  .git  .gitignore  .idea  LICENSE  Pipfile  Pipfile.lock  .python-version  README.md  .venv
  ```
  -(myproject) myproject $ pip freeze > requirements.txt

- file .flake8
  - myproject $ pipenv install flake8
```
[flake8]
max-line-length = 120
exclude = .venv
```

- file .pyup.yml
```
requirements:
  - Pipfile
  - Pipfile.lock
```

- Projeto (myproject) foi criado
  - (myproject) myproject $ django-admin startproject myproject .
```
(myproject) myproject $ tree
.
├── LICENSE
├── manage.py
├── myproject
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── Pipfile
├── Pipfile.lock
├── README.md
└── requirements.txt
```

- Criado app boards
  - (myproject) myproject $ mng startapp boards
```
(myproject) myproject $ tree
.
├── boards
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── LICENSE
├── manage.py
├── myproject
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── Pipfile
├── Pipfile.lock
├── README.md
└── requirements.txt
```

- Criado as migrações
  - (myproject) myproject $ mng makemigrations
```
Migrations for 'boards':
  boards/migrations/0001_initial.py
    - Create model Board
    - Create model Topic
    - Create model Post
```
  - (myproject) myproject $ mng migrate boards 0001
  - (myproject) myproject $ mng migrate 
  - Comandos mnage shell
```
(myproject) myproject $ mng shell
Python 3.8.1 (default, Apr 21 2020, 11:10:26) 
[GCC 7.5.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from boards.models import Board
>>> board = Board(name='Django', description='This is a board about Django.')
>>> board.save()
>>> board.id
1
>>> board.description
'This is a board about Django.'
>>> board.description = 'Django discussion board.'
>>> board.save()
>>> board = Board.objects.create(name='Python', description='General discussion about Python.')
>>> board.id
2
>>> board.name
'Python'
>>> Board.objects.all()
<QuerySet [<Board: Board object (1)>, <Board: Board object (2)>]>
>>> exit()

Summary of Model’s Operations

Find below a summary of the methods and operations we learned in this section, 
using the Board model as a reference. Uppercase Board refers to the class, 
lowercase board refers to an instance (or object) of the Board model class:
Operation 	Code sample
Create an object without saving 	        board = Board()
Save an object (create or update) 	        board.save()
Create and save an object in the database 	Board.objects.create(name='...', description='...')
List all objects 	                        Board.objects.all()
Get a single object, identified by a field 	Board.objects.get(id=1)
```

- Views, Templates, and Static Files
  - myproject/urls.py
```
from django.contrib import admin
from django.urls import path

from boards import views

urlpatterns = [
    path('', views.home, name='home'),
    path('admin/', admin.site.urls),
```

   - boards/views.py
```
from django.http import HttpResponse
from .models import Board

def home(request):
    boards = Board.objects.all()
    boards_names = list()

    for board in boards:
        boards_names.append(board.name)

    response_html = '<br>'.join(boards_names)
    return HttpResponse(response_html)
```   

- Django Template Engine Setup
  - templates/home.html **=> CRIADO NA RAIZ DO PROJETO.**


- Alteração no settings.py => pois o templates foi criado na raiz do projeto
```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [                                 <**
            os.path.join(BASE_DIR, 'templates')   <**   
        ],                                        <**
```  

- Para fazer o teste via Python Shell
```
(myproject) myproject $ mng shell
Python 3.8.1 (default, Apr 21 2020, 11:10:26) 
[GCC 7.5.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from django.conf import settings
>>> settings.BASE_DIR
'/home/plautz/PycharmProjects/myproject'
>>> import os
>>> os.path.join(settings.BASE_DIR, 'templates')
'/home/plautz/PycharmProjects/myproject/templates'
```

- file boards/tests.py
```
from django.urls import reverse, resolve
from django.test import TestCase
from .views import home

class HomeTests(TestCase):
    def test_home_view_status_code(self):
        url = reverse('home')
        response = self.client.get(url)
        self.assertEquals(response.status_code, 200)

    def test_home_url_resolves_home_view(self):
        view = resolve('/')
        self.assertEquals(view.func, home)
```

- Para executar o teste 
  - (myproject) myproject $ python manage.py test
  - (myproject) myproject $ python manage.py test --verbosity=2

- Setup Statis Files
  - criar o diretorio static na raiz do projeto
  - link getbootstrap.com -> copiar o Compiled CSS and JS
  - copy the file css/bootstrap.min.css to our project’s css folder
  - inserir npo settings.py
  ```
  STATIC_URL = '/static/'
  STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static'),]
  ```
  - tempĺates/home.html
  ```
  {% load static %}<!DOCTYPE html>
  <html>
  <head>
    <meta charset="utf-8">
    <title>Boards</title>
    <link rel="stylesheet" href="{% static 'css/bootstrap.min.css' %}">
    ```
  
- habilitar Django Admin
  - (myproject) myproject $ mng createsuperuser

- registrar boards/admin.py
```
from django.contrib import admin
from .models import Board

admin.site.register(Board)
```

- Criar herança de templates.
  - base.html => master page
  - home.html e topics.html => os templates que recebem a herança. (child templates)

- Criar a navbar (top bar with menu) in the base.html
```
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
      <div class="container">
        <a class="navbar-brand" href="{% url 'home' %}">Django Boards</a>
      </div>
    </nav>
```

- Para alterar a letra (estilo de letras) tem p fonte da google
  - https://fonts.google.com/
  - file base.html
  ```
    <head>
    <meta charset="utf-8">
    <title>{% block title %}Django Boards{% endblock %}</title>
    <link href="https://fonts.googleapis.com/css?family=Peralta" rel="stylesheet">
    <link rel="stylesheet" href="{% static 'css/bootstrap.min.css' %}">
    <link rel="stylesheet" href="{% static 'css/app.css' %}">
  </head>
  ```
  
  - criar o diretorio static/css/app.css
  ```
  .navbar-brand {
    font-family: 'Peralta', cursive;
  }
  ```
  
 _ Criar o formulario para receber input de usuario
   - criar um novo URL no myproject/urls.py
     - path('boards/<int:pk>/new/', views.new_topic, name='board_topic'),
   - criar uma função no boards/views.py
     ```
     def new_topic(request, pk):
         board = get_object_or_404(Board, pk=pk)
         return render(request, 'new_topic.html', {'board': board})
     ```
   - criar um templates/new_topic.html
   ```
    {% extends 'base.html' %}

    {% block title %}Start a New Topic{% endblock %}

    {% block breadcrumb %}
      <li class="breadcrumb-item"><a href="{% url 'home' %}">Boards</a></li>
      <li class="breadcrumb-item"><a href="{% url 'board_topics' board.pk %}">{{ board.name }}</a></li>
      <li class="breadcrumb-item active">New topic</li>
    {% endblock %}
    {% block content %}
    {% endblock %}
   ```
   - Rendering Bootstrap Forms
     - myproject $ pipenv install django-widget-tweaks
     - add it to the INSTALLED_APPS => 'widget_tweaks',
     - templates/new_topic.html => {% load widget_tweaks %}
   
   - Reusable Forms Templates

- Implementar Registro, login, logout, password reset, password change
  - (myproject) myproject $ django-admin startapp accounts
  - INSTALLED_APPS => 'accounts',
  - accounts/views.py
  - create the new template, named signup.html
  - accounts/tests.py
  - adding the Email Field to the Form => accounts/forms.py

- improving the Tests Layout
  -  https://www.toptal.com/designers/subtlepatterns/ => procurar um background pattern
  - criar um diretorio static/img => depois de dezipado o file colocar no diretorio img
  - cria o file static/css/accounts.css
  - colocar o {% block stylesheet %} no templates/signup.html
  
- criar logout view
  - myproject/urls.py     => path('logout/', auth_views.LogoutView.as_view(), name='logout'),
  - myproject/settings.py => LOGOUT_REDIRECT_URL = 'home'
  - Mostrar Menu para authenticação de Usuarios
    - download from https://jquery.com/download/ -> compressed, production jQuery 3.2.1
    - links para download
      -  https://code.jquery.com/jquery-3.2.1.min.js
      -  https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.11.0/umd/popper.min.js
      -  https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/js/bootstrap.min.js
  - alterar o templates/base.html => inserir opçoes de menu

- criar login 
  - myproject/urls.py => path('login/', auth_views.LoginView.as_view(template_name='login.html'), name='login'),
  - tmplates/base.html => <a href="{% url 'login' %}" class="btn btn-outline-secondary">Log in</a>
  - criar o templates/login.html
  - criar templates/base_accounts.html => {% extends 'base.html' %}
  - usar o base_accounts.html => signup.html and login.html:
     - templates/login.html => {% extends 'base_accounts.html' %}
     - templates/signup.html => {% extends 'base_accounts.html' %}

- Criar Tags para Template de cliente

  
  
  