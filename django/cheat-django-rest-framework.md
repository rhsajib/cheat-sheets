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



### Swagger API generator

```sh
pip install -U drf-yasg
```

```python
# settings.py

INSTALLED_APPS = [
   ...
   'django.contrib.staticfiles',  # required for serving swagger ui's css/js files
   'drf_yasg',
   ...
]
```

```python
# urls.py
...
from django.urls import re_path
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

...

schema_view = get_schema_view(
   openapi.Info(
      title="My API",
      default_version='v1',
      description="write description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@snippets.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
   path('docs/', schema_view.with_ui('swagger', cache_timeout=0),name='schema-swagger-ui'),

   # extra
   path('swagger<format>/', schema_view.without_ui(cache_timeout=0), name='schema-json'),
   path('redoc/', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
   ...
]
```


### URL patterns

```python
# core/urls.py

urlpatterns = [
    path('admin/', admin.site.urls),
    path('docs/', schema_view.with_ui('swagger', cache_timeout=0),name='schema-swagger-ui'),
    path('api/v1/',
        include([
            path('users/', include('users.urls', namespace='users')),
            path('convert/',
                 include([
                    path('text-to-pdf', include('text_to_pdf.urls', namespace='text_to_pdf'))
                 ])
            )
        ])
    )
]

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



### File handling

```python
# core/settings.py


INSTALLED_APPS = [
    .............
    'rest_framework',
]



# Storage configuration
USE_S3 = config.USE_S3

if USE_S3:
    # aws settings
    AWS_ACCESS_KEY_ID = config.S3_ACCESS_KEY
    AWS_SECRET_ACCESS_KEY = config.S3_SECRET_ACCESS_KEY
    AWS_STORAGE_BUCKET_NAME = config.S3_BUCKET_NAME
    AWS_DEFAULT_ACL = None
    # AWS_S3_SIGNATURE_VERSION = 's3v4'
    # AWS_S3_REGION_NAME = <YOUR AWS S3 BUCKET LOCATION>
    # AWS_S3_FILE_OVERWRITE = False
    # AWS_S3_VERIFY = True
    AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
    AWS_S3_OBJECT_PARAMETERS = {'CacheControl': 'max-age=86400'}

    # s3 static settings
    STATIC_LOCATION = 'static'
    STATIC_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/{STATIC_LOCATION}/'
    STATICFILES_STORAGE = 'hello_django.storage_backends.StaticStorage'

    # s3 public media settings
    PUBLIC_MEDIA_LOCATION = 'media'
    MEDIA_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/{PUBLIC_MEDIA_LOCATION}/'
    DEFAULT_FILE_STORAGE = 'hello_django.storage_backends.PublicMediaStorage'
else:
    STATIC_URL = 'static/'
    STATIC_ROOT = BASE_DIR / 'static'
    MEDIA_URL = 'media/'    
    MEDIA_ROOT = BASE_DIR / 'media'
    DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'

```



```python
# text_to_pdf/models.py

from django.db import models

class FileSizeField(models.PositiveIntegerField):
    description = 'A custom model field for storing file size in bytes.'

    def get_internal_type(self):
        return 'PositiveIntegerField'

class TextToPDF(models.Model):
    uploaded_at = models.DateTimeField(auto_now_add=True)
    file = models.FileField(upload_to='text-to-pdf/')
    file_size = FileSizeField(editable=False, null=True, blank=True)

    def __str__(self):
        return self.file.name

    def save(self, *args, **kwargs):
        # Calculate and update the file size before saving
        self.file_size = self.file.size
        super().save(*args, **kwargs)
    

# class TextToPDF(models.Model):
#     uploaded_at = models.DateTimeField(auto_now_add=True)
#     file = models.FileField(upload_to='text-to-pdf')


#     @property
#     def file_size(self):
#         return self.file.size

```
```python
# text_to_pdf/serializers.py
from rest_framework import serializers
from .models import TextToPDF


class TextToPDFSerializer(serializers.ModelSerializer):
    class Meta:
        model = TextToPDF
        fields = '__all__'
```

```python
# text_to_pdf/views.py

from rest_framework.parsers import FormParser,JSONParser, MultiPartParser
from rest_framework.response import Response
from rest_framework.views import APIView
from rest_framework.generics import CreateAPIView
from rest_framework import status
from .models import TextToPDF
from .serializers import TextToPDFSerializer

class TextToPDFView(CreateAPIView):
    parser_classes = (MultiPartParser,)
    serializer_class = TextToPDFSerializer

    def create(self, request, *args, **kwargs):

        # Access the uploaded file
        file = request.data.get('file', False)
        if not file:
            return Response(
                {'message': 'file key is missing in body'},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        
        # print(file.name)
        # print(file.size)

        print(file)
        # Check if a file was uploaded
        if file:
            # Get all attributes of the file
            file_attributes = file.__dict__
            print(file_attributes)


        # perform checks
        file_ext = str(file.name).split('.')[-1]
        # print('file_ext', file_ext)
        if file_ext not in ['txt',]:
            return Response(
                {
                    'message': 'file not valid' , 
                    'acceptable_extension' : '.txt',
                },
                status=status.HTTP_400_BAD_REQUEST
            )
        
        if file.size > 20 * 1024 * 1024:
            return Response(
                {
                    'message': 'Max size of file is 20 MB',
                    'file_size': f'Current file size is {file.size / (1024 * 1024)}'
                },
                status=status.HTTP_400_BAD_REQUEST
            )
        
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        self.perform_create(serializer)
        print(serializer.data)
        headers = self.get_success_headers(serializer.data)
        return Response(serializer.data, status=status.HTTP_201_CREATED, headers=headers)
```


```python
# text_to_pdf/urls.py
from django.urls import path
from .views import TextToPDFView

app_name = 'text_to_pdf'

urlpatterns = [
    path('', TextToPDFView.as_view(), name='file-upload'),
]
```

```py
# core/urls.py

from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static
from rest_framework import permissions


urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/',
        include([
            path('users/', include('users.urls', namespace='users')),
            path('convert/',
                 include([
                    path('text-to-pdf', include('text_to_pdf.urls', namespace='text_to_pdf'))
                 ])
            )
        ])
    )
]


if settings.DEBUG:
    urlpatterns += static(settings.STATIC_URL, document_root = settings.STATIC_ROOT)
    urlpatterns += static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT)
```







