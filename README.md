# myproject
This project is based on Victor Freitas Django Tutorial

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