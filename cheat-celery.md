### Install celery
```python
~ % pip install celery
```
```python
# project directory

django_project/
    manage.py
    core/
        __init__.py
        settings.py
        celery.py
    taskapp/
        any_file.py

```

```python
# settings.py

CELERY_BROKER_URL = os.environ.get('CELERY_BROKER', 'redis://redis:6379/0')
CELERY_RESULT_BACKEND = os.environ.get('CELERY_BACKEND', 'redis://redis:6379/0')
```

```python
# core/celery.py

import os
from celery import Celery

# Set the default Django settings module for the 'celery' program from manage.py
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'core.settings')

# Create a celery instace
app = Celery('core')                   # 'core' refers directory where settings.py is located 

# The configuration object to child processes.
# namespace='CELERY' means all celery-related configuration keys should have a `CELERY_` prefix.
app.config_from_object('django.conf:settings', namespace='CELERY')

# Load task modules from all registered Django apps.
app.autodiscover_tasks()

@app.task(bind=True, ignore_result=True)
def debug_task(self):
    print(f'Request: {self.request!r}')
    
# ading a task inside of the celery.py file (optional)
@app.task
def add_number():
    return
```

```python
# core/__init__.py
from .celery import app as celery_app

__all__ = ('celery_app',)
```


```python
# taskapp/tasks.py
from celery import shared_task

@shared_task
def add_task(x, y):
    return x + y
```

```python
# taskapp/any_file.py

add_task.delay(4, 5)
```


```python
# starting a Celery worker using the dcelery app and setting the log level to INFO

~ % celery -A dcelery worker --loglevel=INFO
# or
~ % celery --app=dcelery worker -l INFO
```

**Command 1:** `celery -A dcelery worker --loglevel=INFO`
   - `-A dcelery`: Specifies the Celery app to use, which is `dcelery` in this case.
   - `worker`: Indicates that you want to start a Celery worker.
   - `--loglevel=INFO`: Sets the log level for the worker to INFO.

**Command 2:** `celery --app=dcelery worker -l INFO`
   - `--app=dcelery`: Specifies the Celery app to use, which is `dcelery` in this case.
   - `worker`: Indicates that you want to start a Celery worker.
   - `-l INFO`: Short form of `--loglevel=INFO`, which sets the log level for the worker to INFO.




















