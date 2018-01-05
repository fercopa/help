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
# Config struct tests (folder an files)
```bash
rm [appname]/test.py
mkdir [appname]/tests
touch [appname]/tests/__init__.py
touch [appname]/tests/test_models.py
touch[appname]/tests/test_forms.py
touch [appname]/tests/test_views.py
```
# Test models
- Use mixer
## Example mixer
```python
import pytest
from mixer.backend.django import mixer
pytestmark = pytest.mark.django_db


class TestPost:
    def test_init(self):
        obj = mixer.blend(ModelName)
        assert obj.pk == 1, "Should save an instance"
```
# Test views
- Use RequestFactory
## Exaple RequestFactory
```python
from django.test import RequestFactory
from .. import views

class TestHomePage:
  def test_anonymous(self):
      req = RequestFactory().get('/')
      resp = views.HomeView.as_view()(req)
      assert resp.status_code == 200, 'Should be callable by anyone'
```
## views.py
```python
from django.views.generic import TemplateView
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator

class HomeView(TemplateView):
    template_name = '[appName]/home.html'
  
  
class AdminView(TemplateView):
    template_name = '[appName]/admin.html'
  
    @method_decorator(login_required)
    def dispatch(self, request, *args, **kwargs)
        return super(AdminView, self).dispatch(request, *args, **kwargs)
```
- Your tests should pass with 100 % coverage
- This does NOT render the view and test the template
- This does NOT call urls.py

# Test user authenticated

## test_views.py
```python
from django.contrib.auth.models import AnonymousUser
from django.test import RequestFactory
from mixer.backends.django import mixer
pytestmark = pytest.mark.django_db


from .. import views


class TestAdminView:
    def test_anonymous(self):
        req = RequestFactory().get('/')
        resp = views.AdminView.as_views()(req)
        assert 'login' in resp.url
        
    def test_superuser(self):
        user = mixer.blend('auth.User', is_superuser=True)
        req = RequestFactory().get('/')
        req.user = user
        resp = views.AdminView.as_view()(req)
        assert resp.status_code == 200, 'Authenticated user can access'
 ```
