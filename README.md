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
  - criar o package templatetags => file __init__.py and form_tags.py
  - alterar o file templates/includes/form.html
  - testar os tags templates
  
- Password Reset
  - myproject/settings.py 
    - EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
  - configurar rota no file urls.py

-  Criar proteção de Views para usuarios nao autorizados
   - Django has a built-in view decorator to avoid that issue:
     - boards/views.py
     ```
     from django.contrib.auth.decorators import login_required
     @login_required
     def new_topic(request, pk):
         # ...
     ```
   - Configurar Login Next Redirect
   ```
   <form method="post" novalidate>
     {% csrf_token %}
     <input type="hidden" name="next" value="{{ next }}">
     {% include 'includes/form.html' %}
     <button type="submit" class="btn btn-primary btn-block">Log in</button>
   </form>
   ```
  
   - Login Required Tests
     - test_view_home.py will include the HomeTests class (view complete file contents)
     - test_view_board_topics.py will include the BoardTopicsTests class (view complete file contents)
     - test_view_new_topic.py will include the NewTopicTests class (view complete file contents)

  - Accessing the Authenticated User
  
  - Topic Posts View
    - urls;py => path('boards/<int:pk>/topics/<int:topic_pk/', views.topic_posts, name='topic_posts'),
    - boards/views.py
    - templates/topic_posts.html
    ```
    {% extends 'base.html' %}

     {% block title %}{{ topic.subject }}{% endblock %}
    
       {% block breadcrumb %}
         <li class="breadcrumb-item"><a href="{% url 'home' %}">Boards</a></li>
         <li class="breadcrumb-item"><a href="{% url 'board_topics' topic.board.pk %}">{{ topic.board.name }}</a></li>
         <li class="breadcrumb-item active">{{ topic.subject }}</li>
       {% endblock %}
    {% block content %}
    {% endblock %}
    ```
  - Some tests boards/tests/test_view_topic_posts.py
    - run tests => python manage.py test boards
    - run tests => python manage.py test boards.tests.test_view_topic_posts
    - run tests => python manage.py test boards.tests.test_view_topic_posts.TopicPostsTests.test_status_code
  
  - templates/topic_posts.html  
    - baixar free image from   => https://www.iconfinder.com/search/?q=user&license=2&price=free
    - carregado imagens no static/img/
    - alterar avatar templates/topic_posts.html => <img src="{% static 'img/oper2.webp' %}"
    
  - Replay Post View
  
  - QuerySets
    - We have three tasks here:
      - Display the posts count of the board;
      - Display the topics count of the board;
      - Display the last user who posted something and the date and time.
     
     - Define method __str__ for all boards models
     - Via Python Shell terminal
     ```
     (myproject) myproject $ python manage.py shell
      Python 3.8.1 (default, Apr 21 2020, 11:10:26) 
      [GCC 7.5.0] on linux
      Type "help", "copyright", "credits" or "license" for more information.
      (InteractiveConsole)
      >>> from boards.models import Board
      >>> board = Board.objects.get(name='Django')
      >>> board.topics.all()
      <QuerySet [<Topic: Hello everyone>, 
                 <Topic: Teste>, 
                 <Topic: Novo Borad>, 
                 <Topic: >, 
                 <Topic: Ola Jorge>]>
      >>> board.topics.count()
      5
      >>> from boards.models import Post
      >>> Post.objects.all()
      <QuerySet [<Post: This is the first topic. :-)>, 
                 <Post: Teste today>, 
                 <Post: Novo Board croado>, 
                 <Post: >, <Post: >, 
                 <Post: Jorge Tudo bem?>, 
                 <Post: Bom dia Jorge. Tudo bem hoje?>, 
                 <Post: Segund reply>]>
      >>> Post.objects.count()
      8
     ```
    
     - How can we filter them
     ```
     (myproject) myproject $ mng shell
     Python 3.8.1 (default, Apr 21 2020, 11:10:26) 
     [GCC 7.5.0] on linux
     Type "help", "copyright", "credits" or "license" for more information.
     (InteractiveConsole)
     >>> from boards.models import Board, Post
     >>> board = Board.objects.get(name='Django')
     >>> Post.objects.filter(topic__board=board)
     <QuerySet [<Post: This is the first topic. :-)>, 
                <Post: Bom dia Jorge. Tudo bem hoje?>, 
                <Post: Segund reply>, <Post: Teste today>, 
                <Post: Novo Board croado>, 
                <Post: >, <Post: Jorge Tudo bem?>]>
     >>> Post.objects.filter(topic__board=board).count()
     7
     ```
     - The double underscores topic__board is used to navigate through the models’ relationships.
     
     - to identify the last post.
     ```
     >>> Post.objects.filter(topic__board=board).order_by('-created_at')
     <QuerySet [<Post: Segund reply>, 
                <Post: Bom dia Jorge. Tudo bem hoje?>, 
                <Post: Jorge Tudo bem?>, 
                <Post: >, 
                <Post: Novo Board croado>, 
                <Post: Teste today>, 
                <Post: This is the first topic. :-)>]>
     >>> Post.objects.filter(topic__board=board).order_by('-created_at').first()
     <Post: Segund reply>
     ```
     - boards/models -> implementatio
     - templates/home.html
  
  - Melhorar Topic listing view  
  ```
  (myproject) myproject $ mng shell
   Python 3.8.1 (default, Apr 21 2020, 11:10:26) 
   [GCC 7.5.0] on linux
   Type "help", "copyright", "credits" or "license" for more information.
   (InteractiveConsole)
   >>> from django.db.models import Count
   >>> from boards.models import Board
   >>> board = Board.objects.get(name='Django')
   >>> topics = board.topics.order_by('-last_updated').annotate(replies=Count('posts'))
   >>> for topic in topics:
   ...    print(topic.replies)
   ... 
   1
   1
   1
   1
   3
   >>> topics = board.topics.order_by('-last_updated').annotate(replies=Count('posts') - 1)
   >>> for topic in topics:
   ...    print(topic.replies)
   ... 
   0
   0
   0
   0
   2
  ```
  - Alterar boards/views.py
  - Alterar templates/topics.html
  
  - Migrate adicionando a novo campo no Topic model
    - class Topic => views = models.PositiveIntegerField(default=0)
    - (myproject) myproject $ mng makemigrations
    - (myproject) myproject $ mng migrate
    - Alterar boards/views.py
    - Alterar templates/topics.html
  
- Cria GCBV - generic Class Based View
  - Update View
    - alterar boards/views.py => class PostUpdateView(UpdateView):
    - alterar myproject/urls.py 
      - path('boards/<int:pk>/topics/<int:topic_pk>/posts/<int:post_pk>/edit/', 
              views.PostUpdateView.as_view(), name='edit_post'),
    - alterar templates/topic_posts.html => <a href="{% url 'edit_post' post.topic.board.pk post.topic.pk post.pk %}"
    - criar templates/edit_post.html
    - criar file boards/tests/test_view_edit_post.py
  
  - List View
    - Refazer algumas views para aproveitar as vantagens do CBV. Exemplo home.html
    - alterar boards/views.py
    - alterar boards/tests/test_view_home.py => def test_home_url_resolves_home_view(self):
    
  - Paginação - adicionar varios tops.
    ```
    (myproject) myproject $ mng shell
    Python 3.8.1 (default, Apr 21 2020, 11:10:26) 
    [GCC 7.5.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    (InteractiveConsole)
    >>> from django.contrib.auth.models import User
    >>> from boards.models import Board, Topic, Post
    >>> user = User.objects.first()
    >>> board = Board.objects.get(name='Django')
    >>> for i in range(100):
    ...     subject = 'Topic test #{}'.format(i)
    ...     topic = Topic.objects.create(subject=subject, board=board, starter=user)
    ...     Post.objects.create(message='Lorem ipsum...', topic=topic, created_by=user)
    ... 
    ```
    - FBV - Pagination Function-based views
      - alterar boards/views.py => def board_topics(request, pk):
      - alterar templates/topic.html => {% if topics.has_other_pages %}
    
    - GCBV - Generic Class Based View Pagination
      - implementar usando ListView
        - alterar boards/views.py => class TopicListView(ListView)
        - myproject/urls.py => path('boards/<int:pk>/', views.TopicListView.as_view(), name='board_topics'),
        - alterar templates/topic.html => {% if is_paginated %}
     
     - Reusable Paginatiom Template
       - alterar boards/views.py => class TopicListView(ListView)
       - myproject/urls.py => path('boards/<int:pk>/topics/<int:topic_pk>/', views.TopicListView.as_view(), name='topic_posts'),
       - criar file templates/includes/pagination.html
       - alterar templates/topic_posts.html => {% include 'includes/pagination.html' %}
       - alterar templates/topic.html => {% include 'includes/pagination.html' %}
  
  - My Account View
    - inserir no file accounts/views.py => class UserUpdateView(UpdateView):
    - inserir no file myproject/urls.py 
      - path('settings/account/', accounts_views.UserUpdateView.as_view(), name='my_account'),
  
  - Adicionar Markdown
    - instalar lib markdown => myproject $ pipenv install markdown
    - inserir metodo boards/models.py => def get_message_as_markdown(self):
    - alterar templates/topic_posts.html => {{ post.message }} to {{ post.get_message_as_markdown }}
    - alterar templates/reply_topic.html => {{ post.message }} to {{ post.get_message_as_markdown }}
    - Inserir MakDown editor
      - fazer download dos files => simplemde.min.ccs / simplemde.min.js inserir no static
      - Alterar templates/base.html => {% block javascript %}{% endblock %}
      - Alterar templates/reply_topic.html =>  {% block stylesheet %} e {% block javascript %}
      - Alterar templates/edit_topic.html =>  {% block stylesheet %} e {% block javascript %}
  
  - Humanize   
    -  add the django.contrib.humanize to the INSTALLED_APPS
    - alterar templates/topics.html => {% load humanize %}

  - Gravatar - uma maneira facil de adicionar foto no usewr profile
    - no diretorio templatetags criar o file gravatar.py 
    - abortei o precedimento do gravatar pois tem que abrit uma conta no gravatar.
    
  - Ajustes finais
    - 
    
    