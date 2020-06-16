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