```
~ % pip install djangorestframework
```

```python
# settings.py

INSTALLED_APPS = [
    .............
    'rest_framework',
]

```

```python
from rest_framework.response import Response
from rest_framework.decorators import api_view

@api_view(['GET', 'POST'])
def firstAPI(request):
    if request.method == 'POST':
        name = request.data.get('name')
        age = request.data.get('age')
        return Response({'name':name, 'age':age})
    context = {
        'name': 'Rh Sajib',
        'university': 'BUET'
    }
    return Response(context)
```

### Login API
```python
from django.contrib.auth.models import User
from rest_framework.response import Response
from rest_framework.decorators import api_view

@api_view(['POST'])
def regestrationAPI(request):
    if request.method == 'POST':
        username = request.data.get('username') 
        email = request.data.get('email') 
        first_name = request.data.get('first_name') 
        last_name = request.data.get('last_name') 
        password1 = request.data.get('password1') 
        password2 = request.data.get('password2') 

        if User.objects.filter(username=username).exists():
            return Response({'error': 'Username already exists!'})
        
        if password1 != password2:
            return Response({'error': 'Two passwords are not same!'})
        
        user = User()
        user.username = username
        user.email = email
        user.first_name = first_name
        user.last_name = last_name
        user.is_active = True
        user.set_password(raw_password=password1)
        user.save()

        return Response({'success': 'User registration successful!'})
```
#### Token based login
Generate a unique token for each user which will not change
```python
# settings.py

INSTALLED_APPS = [
    .............
    'rest_framework',
    'rest_framework.authtoken',
]
```

```
python manage.py migrate
```

```python
# urls.py
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    path('api/login-token/', obtain_auth_token),
]
```

```python
# form-data

username
password
```

```python
# login using auth token
# headers
Authorization = Token <auth_token>
```



### Simple JWT
```
~ % pip install PyJWT==2.1.0
```

```python
# settings.py

INSTALLED_APPS = [
    .............
    'rest_framework_simplejwt',
]
```
#### Generate refresh & access token
```python
# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('jwt-token/', TokenObtainPairView.as_view()),
    path('jwt-token/refresh', TokenRefreshView.as_view()),
]
```

```python
# form-data
username
password
```


```python
# login using access token
# headers
Authorization = Bearer <access_token>
```

```python
# regenarate access token
# form-data
refresh = <refresh_token>
```

#### Customization of Simple JWT

```python
# settings.py

from datetime import timedelta

SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=120),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=365),
    "ROTATE_REFRESH_TOKENS": False,
    "BLACKLIST_AFTER_ROTATION": False,
    "UPDATE_LAST_LOGIN": False,

    "ALGORITHM": "HS256",
    # "SIGNING_KEY": settings.SECRET_KEY,
    "VERIFYING_KEY": "",
    "AUDIENCE": None,
    "ISSUER": None,
    "JSON_ENCODER": None,
    "JWK_URL": None,
    "LEEWAY": 0,

    "AUTH_HEADER_TYPES": ("Bearer",),
    "AUTH_HEADER_NAME": "HTTP_AUTHORIZATION",
    "USER_ID_FIELD": "id",
    "USER_ID_CLAIM": "user_id",
    "USER_AUTHENTICATION_RULE": "rest_framework_simplejwt.authentication.default_user_authentication_rule",

    "AUTH_TOKEN_CLASSES": ("rest_framework_simplejwt.tokens.AccessToken",),
    "TOKEN_TYPE_CLAIM": "token_type",
    "TOKEN_USER_CLASS": "rest_framework_simplejwt.models.TokenUser",

    "JTI_CLAIM": "jti",

    "SLIDING_TOKEN_REFRESH_EXP_CLAIM": "refresh_exp",
    "SLIDING_TOKEN_LIFETIME": timedelta(minutes=5),
    "SLIDING_TOKEN_REFRESH_LIFETIME": timedelta(days=1),

    "TOKEN_OBTAIN_SERIALIZER": "rest_framework_simplejwt.serializers.TokenObtainPairSerializer",
    "TOKEN_REFRESH_SERIALIZER": "rest_framework_simplejwt.serializers.TokenRefreshSerializer",
    "TOKEN_VERIFY_SERIALIZER": "rest_framework_simplejwt.serializers.TokenVerifySerializer",
    "TOKEN_BLACKLIST_SERIALIZER": "rest_framework_simplejwt.serializers.TokenBlacklistSerializer",
    "SLIDING_TOKEN_OBTAIN_SERIALIZER": "rest_framework_simplejwt.serializers.TokenObtainSlidingSerializer",
    "SLIDING_TOKEN_REFRESH_SERIALIZER": "rest_framework_simplejwt.serializers.TokenRefreshSlidingSerializer",
}
```

```python
from rest_framework_simplejwt.serializers import TokenObtainPairSerializer
from django.conf import settings

class MyTokenObtainPairSerializer(TokenObtainPairSerializer):
    def validate(self, attrs):
        data = super().validate(attrs)
        refresh = self.get_token(self.user)
        data['refresh'] = str(refresh)
        data['access'] = str(refresh.access_token)
        data['type'] = 'Bearer'
        data['lifetime'] = str(settings.SIMPLE_JWT['ACCESS_TOKEN_LIFETIME'].days) + ' days'
        return data
```

```python
from rest_framework_simplejwt.views import TokenObtainPairView
from .serializers import MyTokenObtainPairSerializer

class MyTokenObtainPairView(TokenObtainPairView):
    serializer_class = MyTokenObtainPairSerializer
```

```python
urlpatterns = [

    path('custom-jwt-token/', views.MyTokenObtainPairView.as_view()),
 
]
```












