```python
~ % pip install channels==3.0.5

# if we use channels==4.0.0 we will need to configure more.
```
```
~ % python3 manage.py startapp app
```

```python
# project directory

myproject/
    manage.py
    core/
        __init__.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
    app/
        __init__.py
        consumers.py
        routing.py
        view.py
        urls.py
        templates/
            app/
                index.html

```

```python
# core/settings.py

INSTALLED_APPS = [
    ..........
    'channels',
    'app',
]
```

```python
# core/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('app/', include('app.urls')),
]
```


```python
# app/urls.py

from django.urls import path
from . import views

app_name = 'app'
urlpatterns = [
    path('random/', views.one, name='one'),
]
```

```python
# app/views.py
from django.shortcuts import render

def one(request):
    context = {'words_text': 'ASD'}
    return render(request, 'app/index.html', context=context)
```

```html
# app/index.html

<div>
    <p id="word">{{ words_text }}</p>
</div>

<script>
    const socket = new WebSocket(
            'ws://'
            + window.location.host                  //+ 'localhost:8000'
            + '/ws/'
            + 'any_url'
            + '/'
        );
    
    socket.onmessage = function(event) {
        const data = JSON.parse(event.data);
        console.log(data);
        document.querySelector("#word").innerText = data.message;
    };
</script>
```

```python
# app/routing.py
from django.urls import path
from . import consumers

ws_urlpatterns = [
    path('ws/any_url/', consumers.OneConsumer.as_asgi())
]
```

```python
# app/business.py
import json
from random_word import RandomWords

class Domain:
    def __init__(self):
        self.R = RandomWords()

    def do(self):
        word = self.R.get_random_word()
        return json.dumps({'word': word})




# app/consumers.py
import time
from channels.generic.websocket import WebsocketConsumer
from .business import Domain

class OneConsumer(WebsocketConsumer):
    def connect(self):
        self.accept()

        D = Domain()
        for i in range(100):
            data = D.do()
            self.send(data)
            time.sleep(2)
```

```python
# core/asgi.py
import os
from channels.routing import ProtocolTypeRouter, URLRouter
from django.core.asgi import get_asgi_application
from channels.auth import AuthMiddlewareStack
from app.routing import ws_urlpatterns

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'core.settings')

application = ProtocolTypeRouter(
    {
        'http': get_asgi_application(),
        'websocket': AuthMiddlewareStack(URLRouter(ws_urlpatterns))
    }
)
```

```python
# core/settings.py

WSGI_APPLICATION = 'core.wsgi.application'
ASGI_APPLICATION = 'core.asgi.application'
```


```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```