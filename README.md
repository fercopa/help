# Help for Django project TDD
Contiene ayuda necesaria tanto para configuración como comandos útiles.

# Project setup
  - Install virtualenv
  - Create a new environment
  - Create a new project with _django-admin.py startproject nameproject_
  - Create a test_setting.py
  ```python
    from settings import *
    DATABASES = {
      "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": ":memory:",
        }
    }
    EMAIL_BACKEND = "django.core.mail.backends.locmem.EmailBackend"
  ```
  - Install pytest & plugins and create pytest.ini
  ```bash
  pip install pytest
  pip install pytest-django
  pip install pytest-cov
  pip install mixer
  deactivate
  NameEnvironment/bin/activate
  ```
  pytest.ini
  ```ini
  [pytest]
  DJANGO_SETTINGS_MODULE = NameApp.test_settings
  addopts = --nomigrations --cov=. --cov-report=html
  ```
  - Create .coveragerc
  ```ini
  [run]
  omit =
  *apps.py,
  *migrations/*,
  *settings*,
  *tests/*,
  *urls.py,
  *wsgi.py,
  manage.py
  ```
  * Run with py.test
# Config test
 - Delete [appname]/test.py
 - Create an [appname]/tests folder
 - Create an [appname]/tests/__init__.py
 - Create an [appname]/tests/test_models.py
 - Create an [appname]/tests/test_forms.py
 - Create an [appname]/tests/test_views.py
 
# Example mixer
```python
import pytest
from mixer.backend.django import mixer
pytestmark = pytest.mark.django_db


class TestPost:
    def test_init(self):
        obj = mixer.blend(ModelName)
        assert obj.pk == 1, "Should save an instance"
```
