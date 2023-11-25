### Start a new Django project
```
# demo project directory

- project_name
    - backend
        - core
        - manage.py
    - venv
        - bin
        - include
        - lib
        - pyvenv.cfg
```

```python
# Create a project folder
~ %  mkdir project_name
~ %  cd project_name

# Create Python virtual environment
~ %  python3 -m venv venv

# Activate virtual env
~ %  source venv/bin/activate

# If you want to deactivate virtual env
~ %  deactivate

# Install django
~ %  pip install django~=4.2.0 

# Create 'backend' for project
~ %  mkdir backend
~ %  cd backend

# Create 'core' for backend
~ %  django-admin startproject core .
```

### Create app (from 'backend' folder)
```python
~ %  python manage.py startapp app_name 
```

### Migration
```python
~ %  python manage.py makemigrations 
~ %  python manage.py migrate
```

### Create superuser
```python
~ %  python manage.py createsuperuser
```

### Dotenv

#### python-dotenv
```python
~ %  pip install python-dotenv
```

```python
# backend/.env file

SECRET_KEY=django-insecure-------------------------------
DEBUG=True
ALLOWED_HOSTS=host1,host2,host3
```

```python
# settings.py
import os
from dotenv import load_dotenv
load_dotenv()

SECRET_KEY = os.environ.get('SECRET_KEY')
DEBUG = os.environ.get('DEBUG') == 'True'
ALLOWED_HOSTS = os.environ.get('ALLOWED_HOSTS', '').split(',')  # must be list or tuple  # ['host1', 'host2', 'host3']
```


#### django-dotenv
```python
~ %  pip install django-dotenv
```

```python
# backend/.env file

SECRET_KEY=django-insecure-------------------------------
DEBUG=1
ALLOWED_HOSTS=127.0.0.1
```

```python
# manage.py
import dotenv

def main():
    """Run administrative tasks."""
    dotenv.read_dotenv()          # add this line
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'core.settings')
    .......
```

```python
# settings.py
import os

SECRET_KEY = os.environ.get('SECRET_KEY')
DEBUG = str(os.environ.get('DEBUG')) == '1'             # '1' = True
ALLOWED_HOSTS = os.environ.get('ALLOWED_HOSTS')
```


### Start server
```python
~ %  python manage.py runserver
```
### Changing django port
```python
# using terminal command
~ %  python manage.py runserver 6000
```
or
```python
# manage.py
.......
from django.core.management import execute_from_command_line
from django.core.management.commands.runserver import Command as runserver

runserver.default_port = "6000"
........
```
### Requirements
```python
# Create a requirements file that contain all your projet dependencies
~ %  pip freeze > requirements.txt

# Install your project requirements (if a requirements file exist) 
~ %  pip install -r requirements.txt
```
### Other commands
```python
# Django shell (Run projet code direclty)
~ %  python manage.py shell

# example of code to run in the shell:
 >>> from app_name.models import User
 >>> user1 = User.objects.first()

# Prepare static folders for production
~ %  python manage.py collectstatic

# Take all data from app blog and export in json
~ %  python manage.py dumpdata blog >myapp.json

# Take all data in json file and import in app data table
~ %  python manage.py loaddata myapp.json
```
### Project config
```python
# Add app to settings.py
INSTALLED_APPS = [ … , 'app_name' ]

# Project templates folder: 
create folder projectname/templates

# App templates folder
create folder projectname/templates/appname

# settings.py template config
Project templates settings.py: 
    TEMPLATES = [
        { …
                'DIRS': [BASE_DIR / 'templates', ],
        … }

TIME_ZONE = 'Asia/Dhaka'

# Create Static folder: project_name\static\

# Static folder (settings.py): 
STATIC_URL = '/static/'
STATICFILES_DIRS = [ BASE_DIR / 'static' ] 
STATIC_ROOT = 'static_root'

# To use PostgresSQL
# pip install psycopg2

DATABASE = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'blog',
        'USER': 'admin',
        'PASSWORD': '123456',
        'HOST': 'localhost',
        'PORT': '5432'
        }
    }
```
### Models
```python
# models.py


import uuid
from django.db import models
from django.urls import reverse
from django.utils import timezone
from django.utils.text import slugify
from django.contrib.auth.models import User
from PIL import Image


class Post(models.Model):
    CATAGORY = (                        # Select Field (return value, display value)
        ('teacher', 'Teacher'),
        ('student', 'Student'),
    )

    OPTIONS = (
        ('bangla', 'Bangla'),
        ('english', 'English'),
        ('arabic', 'Arabic'),
    )
    
    # reference
    # https://docs.djangoproject.com/en/4.2/ref/models/fields/
    # null & blank: https://docs.djangoproject.com/en/4.2/ref/models/fields/#null
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, blank=True, null=True, related_name='user_set') # on_delete=models.SET_NULL
    title = models.CharField(verbose_name='Post title', max_length=100, help_text='This is a help text.')
    
    # The id fields is automaticly created by Django for each model. we can customize it if we want
    id = models.AutoField(primary_key=True)
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    
    price = models.DecimalField((max_digits=5, decimal_places=2)
    
    slug = models.SlugField(max_length=100, unique_for_date='created_at')
    slug = models.CharField(max_length=100, default=title, unique=True)

    detail = models.TextField()
    email = models.EmailField()
    age = models.IntegerField()
    salary = models.FloatField()

    created_at = models.DateTimeField(default=timezone.now)
    created_at = models.DateTimeField(auto_now_add=True)
    
    updated_at = models.DateTimeField(auto_now=True)
    publish = models.DateField(auto_now=False, auto_now_add=False, blank=True, null=True)
    is_active = models.BooleanField(default=True)

    catagory = models.CharField(max_length=100, choices=CATAGORY, default='Student')
    image = models.ImageField(default='default.jpg', upload_to='tution/images')

    medium = MultiSelectField(max_length=100 ,choices=OPTIONS, max_choices=3, default='bangla')
    
    subject = models.ManyToManyField(Subject, related_name='subject_set')
    class_in = models.ManyToManyField(Class_in, related_name='class_set')
    
    
    def get_absolute_url(self):
        return reverse('app_name:url_name', kwargs={'slug': self.slug})
        # return '/postdetail/{self.slug}/


    class Meta:
        ordering = ('-created_at',)
        # verbose_name = 'Post'
        verbose_name_plural = 'Posts'
        
        # permissions for user
        permissions = [ ('special_status', 'Can read all posts'), ]


    def save(self, *args, **kwargs):
        # slugify slug field
        if self.slug is None:
            self.slug = slugify(self.title)
        # we can also slugify using django signals (see document's signal section)

        # convert image to desired size
        img = Image.open(self.image.path)
        if img.height > 300 or img.width > 300:
            converted_size = (300, 300)
            img.thumbnail(converted_size)
            img.save(self.image.path)

        super().save(*args, **kwargs)

    # Model string representation
    def __str__(self):
        return self.title
```

#### One To One Relation
```python
from django.contrib.auth.models import User

class ActivationKey(models.Model):
    # A user can create only one post
    user = models.OneToOneField(User, on_delete=models.CASCADE, blank=True, null=True)
```

#### One To Many Relation
```python
from django.contrib.auth.models import User

# A user can create multiple posts (one to many)
user = models.ForeignKey(User, on_delete=models.CASCADE, blank=True, null=True)
```

```python
from django.conf import settings

User = settings.AUTH_USER_MODEL

# A user can create multiple posts (one to many)
user = models.ForeignKey(User, on_delete=models.CASCADE, blank=True, null=True)  # on_delete=models.SET_NULL
```


#### Many To Many Relation
```python
# models.py
from django.db import models

class Subject(models.Model):
    name = models.CharField(max_length=50)
    def __str__(self):
        return self.name

class Class_in(models.Model):
    name = models.CharField(max_length=50)
    def __str__(self):
        return self.name
        
class Post(models.Model):
    subject = models.ManyToManyField(Subject, related_name='subject_set')
    class_in = models.ManyToManyField(Class_in, related_name='class_set')
    def __str__(self):
        return self.title
```



```python
# forms.py
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        exclude = ['user','id', 'slug', 'created_at']
        widgets = {
            'class_in':forms.CheckboxSelectMultiple(attrs={
                'multiple': True
            }),
            'subject':forms.CheckboxSelectMultiple(attrs={
                'multiple': True
            }),
        }
```


```python
# views.py
class PostListView(ListView):
    # model = Post
    template_name = 'tution/postlist.html'
    # queryset = Post.objects.filter(user=1)
    queryset = Post.objects.all().order_by('-id')

    # pass extra data to template
    def get_context_data(self, *args, **kwargs):
        context =  super().get_context_data(*args, **kwargs)
        posts = context.get('object_list')
        context['posts'] = posts
        context['total_posts'] = len(posts)
        context['subjects'] = Subject.objects.all()
        context['class_in'] = Class_in.objects.all()
        return context
```

#### Slug field
```python
# slugify replace spaces with dash '-'
# eleminates all special character
# turn all letters into lowercase

# python manage.py shell

>>> from django.utils.text import slugify
>>> slugify('Hello World, this is amazing!!! cool(£@$)|(@#')
'hello-world-this-is-amazing-cool'  
```

```python
# models.py
from django.utils.text import slugify

class Post(models.Model):
    title = models.CharField(max_length=100)
    slug = models.SlugField(max_length=100, blank=True)
    
    def get_absolute_url(self):
        return reverse('app_name:url_name', kwargs={'slug': self.slug})
        # return '/postdetail/{self.slug}/
    
    # use title as slug field by using slugify
    def save(self, *args, **kwargs):
        self.slug = slugify(self.title)
        super().save(*args, **kwargs)
```

```html
# postlist.html
{% for post in posts %}
    Title: {{post.title}} <br>
    <a href="{{ post.get_absolute_url }}">Detail</a>
{% endfor %}
```

```python
# we can Slugify using django signals
# see this document's signal section
```

#### Multi select field
```python 
# terminal
%  pip install django-multiselectfield

# settings.py
INSTALLED_APPS = [
    ......... ,
    'multiselectfield' ,
]

# models.py
from multiselectfield import MultiSelectField

class Post(models.Model):
    OPTIONS = (
        ('bangla', 'Bangla'),
        ('english', 'English'),
        ('arabic', 'Arabic'),
        ('hindi', 'Hindi'),
        ('urdu', 'Urdu'),
    )
    
    medium = MultiSelectField(max_length=100, choices=OPTIONS, max_choices=3,  default='bangla')
```


#### Image field
```python
# Create media folder: project_name/media
# Create app_name folder inside media folder: project_name/media/app_name/images
# default image: project_name/media/default.jpg
```

```python
# settings.py
MEDIA_ROOT = BASE_DIR/'media'
or, MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

MEDIA_URL = 'media/'
```

```python
# urls.py (root)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    ...............................
] + static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT)
```


```python
# install Pillow to use ImageField  
%  pip install Pillow   
```
```html
# upload_image.html
<form method="post" autocomplete="off" enctype="multipart/form-data">  
    {% csrf_token %}
    {{form|crispy}}
    <button type="submit">Upload</button>
</form>
```


```python
# models.py 
from PIL import Image

class Post(models.Model):
    image = models.ImageField(default='default.jpg', upload_to='app_name/images/')
    
    # convert and save image to desired size (optional)
    def save(self, *args, **kwargs):
        img = Image.open(self.image.path)
        if img.height > 300 or img.width > 300:
            converted_size = (300, 300)
            img.thumbnail(converted_size)
            img.save(self.image.path)
        super().save(*args, **kwargs)
```

```html
# show_image.html 

{% for post in posts %}
    {% if post.image %}
        <img src="{{post.image.url}}" alt="{{post.title}}">
        <!-- or -->
        <img src="/media/{{post.image}}" alt="{{post.title}}">
    {% endif %}
    <h2><a href="">{{ post.title }}</a></h2>
    ......
    ......
{% endfor %}
```

### Signals
Reference: 
- https://www.youtube.com/watch?v=yD33eKfAOg8&ab_channel=CodingEntrepreneurs
- https://docs.djangoproject.com/en/4.2/ref/signals/
- https://docs.djangoproject.com/en/4.2/topics/signals/

```python
# models.py
from django.db.models.signals import pre_save, post_save

class Post(models.Model):
    title = models.CharField(max_length=100)
    slug = models.SlugField(max_length=100, blank=True, null=True)
    email = models.EmailField()
    
    def save(self, *args, **kwargs):
        # do something on different fields
        # slugify slug field
        # modify imafe field

        super().save(*args, **kwargs)

# pre_save function works before save(self, *args, **kwargs) method
# post_save function works after save(self, *args, **kwargs) method


# work flow: ----- pre_save > save > post_save

# inside post_save receiver function
# when we call instance.save(),
# it automatically calls save(self, *args, **kwargs) method
# and before executing save(self, *args, **kwargs) method, it calls pre_save function
# then save(self, *args, **kwargs) executes.
# after executing again post_save function is called
# then instance.save(), 
# then save(self, *args, **kwargs)
# then pre_save function ........
# the above process works recursively 
# And we stop the recursive works by initiating an if condition inside post_save receiver function
# and we add instance.save() inside the if condition.


def ourpost_pre_save(*args, **kwargs):
    print('pre_save')
    print(args, kwargs)

pre_save.connect(ourpost_pre_save, sender=Post)

# pre_save.connect(receiving function, sender = model to send instance)
# that means, connect 'ourpost_pre_save' receiver function everytime any 'Post' instance is saved 

  
def ourpost_post_save(*args, **kwargs):
    print('post_save')
    print(args, kwargs)

post_save.connect(ourpost_post_save, sender=Post)
```

```python
# models.py
class Post(models.Model):
    title = models.CharField(max_length=100)
    slug = models.SlugField(max_length=100, blank=True, null=True)
    email = models.EmailField()

# reference:
# https://www.youtube.com/watch?v=iWie_2I1_9M&t=630s

"""------------process 1---------------"""

# slugify using pre_save
def ourpost_pre_save(sender, instance, *args, **kwargs):
    # print(sender, instance)
    if instance.slug is None:
        instance.slug = slugify(instance.title)
pre_save.connect(ourpost_pre_save, sender=Post)


# slugify using post_save
def ourpost_post_save(sender, instance, created, *args, **kwargs):
    print(sender, instance, created)
    if created or instance.slug is None: 
        instance.slug = slugify(instance.title)
        # 'ourpost_pre_save()' function will be invoked before 'instance.save()'
        instance.save()
post_save.connect(ourpost_post_save, sender=Post)

# so in the above,
# at first, slug will be 'this is default slug'
# then, before save slug will be 'slugify(instance.title)'



"""------------process 2---------------"""
from django.dispatch import receiver


@receiver(ourpost_post_save, sender=Post)
def ourpost_post_save(sender, instance, created, *args, **kwargs):
    if created or instance.slug is None: 
        instance.slug = slugify(instance.title)
        instance.save()
```
#### Generate secret keys during user creation
Process - 1 

```python
# models.py
from django.db import models
from django.contrib.auth.models import User
from django.core.management.utils import get_random_secret_key
from django.db.models.signals import post_save
from django.dispatch import receiver

class ActivationKeys(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, blank=True, null=True)

    admin = models.CharField(max_length=100, blank=True, null=True)
    teacher = models.CharField(max_length=100, blank=True, null=True)
    student = models.CharField(max_length=100, blank=True, null=True)
    
    class Meta:
        verbose_name_plural = 'Activation Keys'

    def __str__(self):
        return self.user.username

    def save(self, *args, **kwargs):
        self.admin = get_random_secret_key()
        self.teacher = get_random_secret_key()
        self.student = get_random_secret_key()
        super().save(*args, **kwargs)
        

# @receiver(post_save, sender=User)
def post_signals(sender, instance, created, *args, **kwargs):
    if created:
        ActivationKeys.objects.create(user = instance)
post_save.connect(post_signals, sender=User, dispatch_uid='my_unique_identifier')
```
Process - 2 (creating 'signals.py' file)

```python
# here, app_name is 'accounts'

# accounts/models.py
from django.db import models
from django.contrib.auth.models import User
from django.core.management.utils import get_random_secret_key
from django.db.models.signals import post_save
from django.dispatch import receiver

class ActivationKeys(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, blank=True, null=True)

    admin = models.CharField(max_length=100, blank=True, null=True)
    teacher = models.CharField(max_length=100, blank=True, null=True)
    student = models.CharField(max_length=100, blank=True, null=True)
    
    class Meta:
        verbose_name_plural = 'Activation Keys'

    def __str__(self):
        return self.user.username

    def save(self, *args, **kwargs):
        self.admin = get_random_secret_key()
        self.teacher = get_random_secret_key()
        self.student = get_random_secret_key()
        super().save(*args, **kwargs)
```


```python
# accounts/signals.py
from django.contrib.auth.models import User
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import ActivationKeys


@receiver(post_save, sender=User)
def post_signals(sender, instance, created, *args, **kwargs):
    if created:
        ActivationKeys.objects.create(user = instance)

# post_save.connect(post_signals, sender=User, dispatch_uid='my_unique_identifier')
```

```python
# accounts/apps.py
from django.apps import AppConfig

class AccountsConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'accounts'

    # add the following code
    def ready(self):
        from . import signals
        # import accounts.signals
```

### Forms
```python
# models.py
from django.db import models

class Contact(models.Model):
    name = models.CharField(max_length=100)
    phone = models.CharField(max_length=17)
    title = models.CharField()

    def __str__(self):
        return self.name
```

#### Using forms.Form
```python
# forms.py
from django import forms
from django.core.exceptions import ValidationError
from .models import Contact

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100, label='Your Name', widget=forms.TextInput(attrs={'class': 'form-control', 'placeholder':'Enter Name'}))
    phone = forms.CharField(max_length=100, label='Your Phone')
    title = forms.CharField(max_length=100, label='Your Title')
    
    # optional (if we want to get any clean field)
    def clean_title(self):                  # def clean_fieldname (self):
        cleaned_data = self.cleaned_data    # dictionary that contains only 'title' field
        # print('cleaned_data', cleaned_data)
        title = cleaned_data.get('title')
        
        # do some operations on 'title' field
        if 'the office' in title.lower():
            raise forms.ValidationError('This title is taken.')  # raise field error
        
        print(title)
        return title
        
    # optional (if we want to get all clean field)
    def clean(self):
        cleaned_data = self.cleaned_data    # dictionary that contains all fields
        # print('all clean_data', clean_data)
        
        title = cleaned_data.get('title')
        
        # do some operations on 'title' field
        if 'the office' in title.lower():
            self.add_error('title', 'This title is taken.') # raise field error
            
            # here, if we use
            # raise forms.ValidationError('This title is taken.') inside 'clean(self)' method
            # it will raise a non field error
            # we can check it in browser inspect
        return clean_data
        
    # after the above operation 'form.is_valid()' function in views.py will be invoked
```

```python 
# views.py
from django.shortcuts import render
from .models import Contact
from .forms import *

def contact_with_form(request):
    initials = {
            'phone': '+880',
            'content': 'My content is'
        }
        
    form = ContactForm(initial=initials)
    
    context = {'form': form}
    
    if request.method == 'POST':
        form = ContactForm(request.POST)
        # print(dir(form))
        
        context[form] = form
        
        # if no validations error occur 'form.is_valid()' will be invoked
        if form.is_valid():
            # print(cleaned_data)
            name = form.cleaned_data['name']  # or, name = form.cleaned_data.get('name')
            phone = form.cleaned_data['phone']
            content = form.cleaned_data['content']
            
            obj = Contact(name=name, phone=phone, content=content)
            obj.save()
            
            # or
            obj = Contact.objects.create(name=name, phone=phone, content=content)
            
            context['object'] = obj
            context[created] = True
   
    return render(request, 'contact.html', context=context)
```

#### Using forms.ModelForm

```python
# https://docs.djangoproject.com/en/4.2/ref/forms/fields/ 

# forms.py
from django import forms
from django.core.exceptions import ValidationError
from .models import Contact

class ContactModelForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = ['title', 'content']       # '__all__'
        
        # add some conditions for title field
    def clean_title(self):                      # def clean_fieldName(self):
        # self.cleaned_data = dictionary that contains only 'title' field
        # print('cleaned_data', self.cleaned_data)

        value = self.cleaned_data.get('title')   # or, value = self.cleaned_data['title']

        num_of_words = value.split(' ')
        if len(num_of_words) > 6:
            self.add_error('title', 'Title can have maximum 6 words')
        return value
        
     def clean_title(self):

        # instance = getattr(self, 'instance', None)
        # print('instance = ',instance)
        # print('instance.pk', instance.pk)


        # Check if the form is being updated

        # process-1
        title = self.cleaned_data.get('title')
        if self.instance.pk:
            return title  # Skip the validation for title when updating
        
        # process-2
        # title = self.cleaned_data.get('title')
        # instance = getattr(self, 'instance', None)  # Get the instance being updated
        # if instance and instance.title == title:
        #     # Skip validation if the title hasn't changed
        #     return title

        queryset = Post.objects.filter(title__icontains=title)
        if queryset.exists():
            self.add_error('title', f'Title \"{title}\" is already taken.')
        return title
        
        
    # optional (if we want to get all clean field)
    def clean(self):
        cleaned_data = self.cleaned_data       # dictionary that contains all fields
        # print('all clean_data', clean_data)
        
        # do some operations on 'title' field
        title = cleaned_data.get('title')
        queryset = Contact.objects.filter(title__icontains=title)
        
        if queryset.exists():
            self.add_error('title', f'This \"{title}\" is already taken.') # raise field error
            
            # here, if we use
            # raise forms.ValidationError('This title is taken.') inside 'clean(self)' method
            # it will raise a non field error
            # we can check it in browser inspect
           
        return clean_data
```        

```python
# views.py
from django.shortcuts import render
from .forms import ContactModelForm

def contact_with_modelform(request):
    form = ContactModelForm(request.POST or None)
    if form.is_valid():
        form.save()
    return render(request, 'contact.html', {'form': form})
```

```python
# another views.py
from django.shortcuts import render
from .forms import ContactModelForm

def contact_with_modelform(request):
    form = ContactModelForm(request.POST or None)
    context = {'form': form}
    if form.is_valid():
        contact_object = form.save()
        context['form'] = ContactModelForm()    # render a new form
        context['object'] = contact_object
        context['created'] = True
    return render(request, 'contact.html', context=context)
```
#### Render form in template
```html
# Render form in template 'contact.html'
<form method=“post” action=“” novalidate> 
    {% csrf_token %}
    {{ form }} 
    
    {{ form.as_div }} 
    {{ form.as_table }}
    {{ form.as_ul }}
    
    <!-- show single field -->
    {{ form.name.label_tag }}
    {{ form.name }}
    {{ form.name.errors }}      <!-- show errors for a field -->
    {{ form.name.help_text}}    <!-- show help -->
    
    <!-- use for loop -->
    {% for field in form %}
        {{ field.errors }}
        {{ field.label_tag }}
        {{ field }}
        {{ field.help_text }}
    {% endfor %}
    
    <input type="submit" value="Submit">
</form>
```

#### Design form in template 
```python
# forms.py
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100, label='Your Name', widget=forms.TextInput(attrs={'class': 'form-control', 'placeholder':'Enter Your Name'}))
    phone = forms.CharField(max_length=100, label='Your Phone')
    content = forms.CharField(max_length=100, label='Your Details')
```

```python
# forms.py
from django import forms
from django.contrib.auth import get_user_model
from django.contrib.auth.forms import UserCreationForm
from django.utils.translation import gettext_lazy as _


User = get_user_model()
class SignUpForm(UserCreationForm):

    username = forms.CharField(
        label='',
        strip=False,
        widget=forms.TextInput(attrs={'class': 'form-control', 'placeholder': _('Username')}),
    )

    password1 = forms.CharField(
        label='',
        strip=False,
        widget=forms.PasswordInput(attrs={'class': 'form-control', 'placeholder': _('Password')}),
    )
    password2 = forms.CharField(
        label='',
        strip=False,
        widget=forms.PasswordInput(attrs={'class': 'form-control', 'placeholder': _('Confirm Password')}),
    )

    class Meta:
        model = User
        fields = ('username', 'password1', 'password2')
```
```python
# forms.py
class ContactModelForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = '__all__'

        widgets = {
            'name': forms.TextInput(attrs={'class':'form-control', 'placeholder':'Enter Your Name'}),
            'phone': forms.TextInput(attrs={'class':'form-control', 'placeholder':'Enter Your Phone'}),
            'content': forms.Textarea(attrs={'class':'form-control', 'placeholder':'Say Something', 'rows': 5}),
        }

        labels = {
            'name': 'Your Name',
            'phone': 'Phone Number',
            'content': 'Write Content',
        }

        help_texts = {
            'name': 'Your Full Name',
            'phone': 'Personal Phone Number',
            'content': 'Let Us Know Something',
        }
        
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fields['name'].label =  'My Name'   # changeb field name
        self.fields['phone'].initial =  '+880'    # initialize a field
        self.fields['content'].initial =  'My content is'
    


# contact.html
<form method=“post” action=“” novalidate> 
    {% csrf_token %}
    {{ form }} 
    <input type="submit" value="Submit">
</form> 
```


#### Use Crispy-forms 
[Django - Advanced Fullstack App (HR PRO)](https://www.youtube.com/playlist?list=PLEoCKCuqTqMh4WH-xXmDlQqIZmuDF09ya)

[crispy-bootstrap5](https://github.com/django-crispy-forms/crispy-bootstrap5)
```python
# For Bootstrap view
pip install django-crispy-forms
pip install crispy-bootstrap5
```

```python
# settings.py
INSTALLED_APPS = (
    ...
    "crispy_forms",
    "crispy_bootstrap5",
    ...
)

CRISPY_ALLOWED_TEMPLATE_PACKS = "bootstrap5"
CRISPY_TEMPLATE_PACK = "bootstrap5"
```

```python
# contact.html
{% load crispy_forms_tags %}
{{ form|crispy }}

<--! show single field -->
{{ form.name | as_crispy_field}}

{{ form.email|as_crispy_field }}
```


[crispy-tailwind](https://github.com/django-crispy-forms/crispy-tailwind)

```python
# crispy-tailwind
pip install django-crispy-forms
pip install crispy-tailwind
```

```python
# settings.py
INSTALLED_APPS = (
    ...
    "crispy_forms",
    "crispy_tailwind",
    ...
)

CRISPY_ALLOWED_TEMPLATE_PACKS = "tailwind"
CRISPY_TEMPLATE_PACK = "tailwind"
```

```python
# template usage
{% load crispy_forms_tags %}
{% load tailwind_filters %}
{{ form|crispy}}
```

### Admin panel
```python
# app_name/admin.py
from .models import Blog
admin.site.register(Blog)
```
#### Customize Admin Panel
```python
# app_name/admin.py
from django.contrib import admin
from .models import Contact, Post, Subject, Class_in

class PostAdmin(admin.ModelAdmin):
    fields = ['title', 'catagory']             # Fields to use for add/edit/show page
    list_display = ['id', 'title', 'catagory'] # Fields to display in search page
    list_display_links = ["title"]             # fields that will be a link in search page
    ordering = ['id']                          # Ordering allowed in the search page
    search_fields = ['title', 'catagory']      # Search fields allowed in the search page
    list_per_page = 10

# Register app
admin.site.register(Post, PostAdmin)
```
### Routing
```python
# project_folder/urls.py (root urls path)
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls), # pre-created admin urls routes
    path('', include('app_name.urls', namespace='app_name')) # include your app urls
]
```
```python
# Urls.py route declaration
path('<int:pk>/update/', PostsUpdateView.as_view(), name='post-update')
```

```python
# app_name/urls.py
from django.urls import path
from . import views

app_name = 'app_name'
url patterns = [ 
    path('posts', views.index, name='index'),
    path('posts/create/', views.create, name='create',
    path('posts/<int:id>/', views.show, name='show'),
    path('posts/<int:pk>/edit/', views.edit, name='edit'),
    path('<uuid:id>', views.detail, name='detail'),
    path('posts/<str:slug>/', views.delete, name='delete'),
] 
```
```html
<a href="{% url 'app_name:path_name' post.id% }">Edit</a>
<a href="{% url 'app_name:path_name' slug=post.slug %}">Delete</a>
<img src="{{post.image.url}}" alt="">
```

### Static route
```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

### Media file
```python
# Create media folder: project_name/media
# Create app_name folder inside media folder: project_name/media/app_name/

# settings.py
MEDIA_ROOT = BASE_DIR/'media'
or, 
import os
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

MEDIA_URL = 'media/'

# urls.py (root)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    ...............................
]
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT)
```

### Function Based Views
```python
# views.py
from django.shortcuts import render, redirect
from .models import Post
from .forms import PostForm

def index(request):
    # Get all Posts
    posts = Post.objects.all()

    # Render app template with context
    return render(request, 'appfolder/index.html', {'posts': posts})

@csrf_exempt               # exempt from the need of csrf token for post request
def show(request, id):
    post = Post.objects.get(id=id)
    return render(request, 'appfolder/show.html', {'post': post})

def create(request):
    # print(request.POST)
    form = PostForm(request.FILES, request.POST or None)  # request.FILES is for image field
    if form.is_valid():
        # optionally we can access form data with form.cleaned_data['first_name']  
        post = form.save(commit=False)   # commit=False as we are not taking user as input
        post.user = request.user
        object = post.save()
        
        return redirect('tuition:post_detail', slug=object.slug)
        # or
        return redirect(post.get_absolute_url())

    return render(request, 'appfolder/create.html', {'form': form})

def edit(request, id):
    post = Post.objects.get(id=id)
    form = PostForm(request.POST or None, instance=post)
    if form.is_valid():
        form.save()
        return redirect('/post')

    return render(request, 'appfolder/edit.html', {'form': form})

def delete(request, id):
    post = Post.objects.get(id=id)
    post.delete()
    return redirect('tuition:postlist')
```


### Class Based Views (CBV)

```python
# for_my_reference, pc_path = Tech solutions BD/practice1
# views.py
from django.views import View
from .forms import ContactModelForm

class ContactView(View):
    form_class = ContactModelForm
    template_name = 'contact.html'
    def get(self, request, *args, **kwargs):
        form = self.form_class()
        return render(request, self.template_name, {'form': form})

    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)

        if form.is_valid():
            form.save()
            return HttpResponse("Success")
        
        return render(request, self.template_name, {'form': form})

# urls.py
urlpatterns = [
    ....
    path('contact/', views.ContactView.as_view(), name='ContactView'),
]
```
We can use the above CBV to render another form and template without creating a new view
```python
# urls.py
from .forms import ContactModelFormAnother

urlpatterns = [
    ....
    path('contact/', views.ContactView.as_view(), name='ContactView'),
    path(
    'contact_another/', 
    views.ContactView.as_view(
        form_class=ContactModelFormAnother, 
        template_name = 'contact_another.html'
    ), 
    name='ContactModelFormAnother'),
]

# So far, we have created two urls using one CBV
```


```python
# models.py
from django.views.generic import (
    TemplateView, 
    ListView, 
    DetailView, 
    CreateView, 
    UpdateView, 
    DeleteView
)
```

#### TemplateView
```python

class LandingPageView(TemplateView):
    template_name = 'landing.html'

    # Optional: To send data to template, change context data dict
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['title'] = 'Landing Page'
        return context
        # we can grab context data in html by using {{title}}

# use TemplateView directly in urls.py without creating LandingPageView
# urls.py
from django.views.generic import TemplateView

urlpatterns = [
    ....
    path('landing/', views.TemplateView.as_view(template_name = 'landing.html'), name='landing'),
]
```


#### ListView
```python
class PostsListView(ListView):
    template_name = 'tution/postlist.html'
    queryset = Post.objects.all()
    # context = {'object_list': queryset}
    # so context variable name in template is 'object_list'
 
# postlist.html
{% for p in object_list %}
    {{p.title}} <br>
{% endfor %}  


class PostsListView(ListView):
    template_name = 'tution/postlist.html'
    # if we don't use queryset, we can mention model name
    # to pass model data as 'object_list' in context 
    model = Post
    
    # change context key 'object_list'
    context_object_name = 'posts'    (default: object_list)

# postlist.html
{% for p in posts %}
    {{p.title}} <br>
{% endfor %}


class PostListView(ListView):
    template_name = 'tution/postlist.html'
    queryset = Post.objects.filter(user=1)
    
    # Optional: To send data to template, change context data dict
    def get_context_data(self, *args, **kwargs):
        context =  super().get_context_data(*args, **kwargs)
        context['posts'] = context.get('object_list')
        context['message'] = 'This is post list'
        return context
```

#### DetailView
```python
class PostsDetailView(DetailView):
    model = Post 
    template_name = 'tution/postdetail.html'
    # context = {'object': object}
    # so context variable name in template is 'object'

# postdetail.html
<div>
    Title: {{object.title}}
</div>
```
```python
class PostsDetailView(DetailView):
    model = Post 
    template_name = 'tution/postdetail.html'
    
    # pass extra data to template
    def get_context_data(self, *args, **kwargs):
        context =  super().get_context_data(*args, **kwargs)
        context['post'] = context.get('object')
        context['post_dict'] = context.get('object').__dict__
        context['message'] = 'This is post details'
        return context

# postdetail.html
<div>
    Title: {{post.title}}
    {% for key, value in post_dict.items %}
        {{key.capitalize}}  :   {{value}} <br>
    {% endfor %}
</div>
```
```python
# urls.py
urlpatterns = [
    .........
    path('postdetail/<int:pk>/', views.PostsDetailView.as_view(), name='PostsDetailView'),
]
or, 
urlpatterns = [
    .........
    path('postdetail/<str:slug>/', views.PostsDetailView.as_view(), name='PostsDetailView'),
]
```

#### CreateView
```python
from django.urls import reverse

class PostsCreateView(CreateView):
    form_class = PostForm
    template_name = 'posts/post_create.html' # no default value

    def get_success_url(self):
        return reverse('posts-list')

    # Optional: Overwrite form data (before save)
    def form_valid(self, form):
        if self.request.user.is_authenticated:
            form.instance.author = self.request.user

        return super().form_valid(form)
```

#### UpdateView
```python
from django.urls import reverse_lazy

class PostUpdateView(UpdateView):
    model = Post
    form_class = PostForm
    template_name = 'tution/postcreate.html'

    def get_success_url(self):
        id = self.object.id
        return reverse_lazy('tution:PostsDetailView', kwargs={'pk':id})

    # Optional: Change context data dict
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['submit_text'] = 'Update'
        return context

# urls.py
urlpatterns = [
    ........
    path('postupdate/<int:pk>/', views.PostUpdateView.as_view(), name='PostUpdateView'),
]

```

#### DeleteView
```python
class PostDeleteView(DeleteView):
    model = Post
    template_name = 'tuition/postdelete.html'
    success_url = reverse_lazy('tuition:PostListView')
    
# urls.py
urlpatterns = [
    ...........
    path('postdelete/<int:pk>/', views.PostDeleteView.as_view(), name='PostDeleteView'),
]

# postdelete.html
<form method="post">
    {% csrf_token %}
    Do you want to delete your post? <br>
    <button type="submit" class="btn btn-outline-info mt-2">Yes</button>
</form>
<button type="button" class="btn btn-outline-info mt-2" onclick="location.href='{% url 'tution:PostListView' %}'">No</button>

```
#### FormView
```python
from django.views.generic.edit import FormView
from django.urls import reverse_lazy
from .forms import ContactModelForm

class ContactView(FormView):
    form_class = ContactModelForm
    template_name = 'contact.html'
    success_url = '/'                # root url
    
    def form_valid(self, form):
        form.save() 
        messages.success(self.request, 'Contact successfully submitted !!')
        return super().form_valid(form)
        
    def form_invalid(self, form):
        .............
        .............
        return super().form_invalid(form)
        
    # Instead of defining success_url attribute
    # we can use get_success_url method 
    
    def get_success_url(self):
        return reverse_lazy('url_name')
```
### Permissions
#### LoginRequiredMixin
```python
# books/views.py 
from django.contrib.auth.mixins import LoginRequiredMixin 
from django.views.generic import ListView, DetailView

from .models import Book

class BookListView(LoginRequiredMixin, ListView): # new 
    model = Book 
    context_object_name = "book_list" 
    template_name = "books/book_list.html" 
    login_url = "account_login" # new


class BookDetailView(LoginRequiredMixin, DetailView):  # new
    model = Book 
    context_object_name = "book" 
    template_name = "books/book_detail.html" 
    login_url = "account_login" # new
```

#### Custom Permissions
##### User Permissions
##### PermissionRequiredMixin
##### Groups & UserPassesTestMixin

### Django Template
- Templates are store in `project_folder/templates` 
- or in `app_folder/templates/app_name/*.html`
```python
{% extends 'base.html' %}
{% block content %}     
{% endblock %} 

{% include 'header.html' %} 

{% if user.username = 'Mike' %}
    <p>Hello Admin</p>   
{% else %}   
    <p>Hello User</p>
{% endif %} 

{% for product in products %}   
  <p>The product name is {{ product }}<p>
{% endfor %} 

{{ var_name }}

Template variables formating
{{ title | lower }} 
{{ blog.post | truncatwords:50 }}
{{ order.date | date:"D M Y" }}
{{ list_items | slice:":3" }}
{{ total | default:"nil" }}

Current path (ex. posts/1/show)
{{ request.path }}   

URL by name with param
<a href="{% url 'app_name:name' post.id %}">{{-------}}</a>

<form method="post" action="{% url 'app_name:name' %}">
  {% csrf_token %}
  <input name="search" placeholder="Search">
  <button class="btn btn-outline-primary" type="submit">Submit</button>
</form>


Use static in template: 
{% load static %}
{% static 'css/main.css' %} 
```


### Messages
https://docs.djangoproject.com/en/4.2/ref/contrib/messages/
```python
# views.py
from django.contrib import messages

# for class based view
messages.success(self.request, 'Contact successfully submitted !!')

# for function based view
messages.success(request, 'Submitted Successfully !')

# Message tags
# debug, info, success, warning and error

# Display in template 
{% if messages %} 
    {% for message in messages %} 
        {% message %} 
        {% message.tags %}

```
```html
<!-- demo.html -->
{% for message in messages %}
<div class="alert alert-{{message.tags}} alert-dismissible fade show" role="alert">
    {{message}}
    <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
</div>
{% endfor %}
```

### Show date time
https://docs.djangoproject.com/en/4.2/ref/templates/builtins/#date
```html
{% now "h:i A" %}
{% now "D jS F Y" %}
```

### Humanize time
```python
# settings.py
INSTALLED_APPS = [
    ...........
    'django.contrib.humanize',
]

# postdetail.html
{% load humanize %}
Created at: {{post.created_at}}   # Created_at : July 9, 2023, 3:55 a.m.
Created at: {{post.created_at | naturaltime}}   # Created at: 1 day, 10 hours ago
```

### Search
```html
# search.html
<form role="search" method="post" action="{% url 'tution:search' %}">
  {% csrf_token %}
  <input type="search" name="search" placeholder="Search">
  <button type="submit">Search</button>
</form>
```

#### Process - 1

```python
# views.py
from django.db.models import Q

def post_search(request):
    query = request.POST.get('search')
    queryset = []
    
    if query is not None:
        lookups = Q(title__icontains=query) | Q(detail__icontains=query) | Q(user__username__icontains=query)
    
        # Q(user__username__icontains=query)
        # here, user = model field
        # user is a foreign key of User model
        # so, username = field of User model
    
        queryset = Post.objects.filter(lookups).distinct()
        # or queryset = Post.objects.filter(title__icontains=query).distinct()

    context = {
        'results' : queryset,
        'results_count' : queryset.count(),
        'query' : query,
        }

    return render(request, 'tution/search.html', context)
```

#### Process - 2 (using model manager)

```python
# models.py
from django.db.models import Q

class PostManager(models.Manager):
    def search(self, query=None):
        if query is None or query == '':
            return self.get_queryset().none()           # Same as, Post.objects.none() which is empty query list
        lookups = Q(title__icontains=query) | Q(detail__icontains=query) | Q(user__username__icontains=query)
        return self.get_queryset().filter(lookups)      # Same as, return Post.objects.filter(lookups)
        
class Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, blank=True, null=True)
    title = models.CharField(max_length=100)
    detail = models.TextField()
    
    objects = PostManager()
    
    
    
# views.py   
def post_search(request):
    query = request.POST.get('search')
    queryset = Post.objects.search(query=query)
        
    context = {
        'results' : queryset,
        'results_count' : queryset.count(),
        'query' : query,
        }

    return render(request, 'tution/search.html', context=context)  
```

#### Process - 3 (using custom QuerySet)

```python
# models.py
from django.db.models import Q

class PostQuerySet(models.QuerySet):
    def search(self, query=None):
        if query is None or query == '':
            return self.none()           # []
        lookups = Q(title__icontains=query) | Q(detail__icontains=query) | Q(user__username__icontains=query)
        return self.filter(lookups) 
        

class PostManager(models.Manager):
    def get_queryset(self):
        return PostQuerySet(self.model, using=self._db)
        
    def search(self, query=None):
        return self.get_queryset().search(query=query) 
        
        
class Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, blank=True, null=True)
    title = models.CharField(max_length=100)
    detail = models.TextField()
    
    objects = PostManager()
    
    
    
# views.py   
def post_search(request):
    query = request.POST.get('search')
    queryset = Post.objects.search(query=query)
        
    context = {
        'results' : queryset,
        'results_count' : queryset.count(),
        'query' : query,
        }

    return render(request, 'tution/search.html', context=context)  
```


```html
# search_result.html

<h5>Total {{results_count}} results found for '{{query}}'</h5>
{% for result in results %}
    <a href="{% url 'tution:PostsDetailView' result.id %}">{{result}}</a>
{% endfor %}
```

#### Process - 4 (using CBV)
```python
# books/views.py 
class SearchResultsListView(ListView):
    model = Book 
    context_object_name = "book_list" 
    template_name = "books/search_results.html" 
    queryset = Book.objects.filter(title__icontains="beginners")
    
############# or #############
# books/views.py 
from django.db.models import Q

class SearchResultsListView(ListView):
    model = Book 
    context_object_name = "book_list" 
    template_name = "books/book_list.html"

    def get_queryset(self):
        query = self.request.GET.get("q") 
        return Book.objects.filter( 
            Q(title__icontains=query) | Q(author__icontains=query) 
        )
```



### User Model


```python
# Get a reference to Django pre-created User model
from django.contrib.auth import get_user_model

User = get_user_model()
```

```python
from django.conf import settings

User = settings.AUTH_USER_MODEL
```

#### AbstractUser vs AbstractBaseUser
The default user model in Django uses a username to uniquely identify a user during authentication. If you'd rather use an email address, you'll need to create a custom user model by either subclassing AbstractUser or AbstractBaseUser.

Options:
- AbstractUser: Use this option if you are happy with the existing fields on the user model and just want to remove the username field.
- AbstractBaseUser: Use this option if you want to start from scratch by creating your own, completely new user model.


#### using AbstractUser

```python
# models.py
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser): 
    mobile = models.CharField(max_length=30)

# To make Django use that model go to settings.py and add: AUTH_USER_MODEL = 'app_name.CustomUser'
```
```python
# settings.py

AUTH_USER_MODEL = 'app_name.CustomUser'
```

#### Using AbstractUser class
```python
# models.py
from django.db import models
from django.contrib.auth.models import AbstractUser
from django.utils.translation import gettext_lazy as _

class CustomUser(AbstractUser):
    email = models.EmailField(_("email address"), unique=True,)
    email_token = models.CharField(max_length=100, blank=True, null=True)
    mobile = models.CharField(max_length=30, blank=True, null=True)


    # define field required for admin login
    USERNAME_FIELD = 'email'  
    
    # define additional fields required to create superuser 
    REQUIRED_FIELDS = ['mobile', 'username']   
    # so, to create superuser 
    # required fields are
    # 'email', 'mobile', 'username', 'password'

    def __str__(self):
        return self.username
```

```python
# settings.py

AUTH_USER_MODEL = 'app_name.CustomUser'
```

```python
# admin.py
from django.contrib import admin
from .models import CustomUser

admin.site.register(CustomUser)
```

```python
python manage.py makemigrations
python manage.py migrate

# if it causes any error,
# delete all 'migrations' folder
# delete 'db.sqlite3' file
# then run the following commands

python manage.py makemigrations useraccounts
python manage.py migrate useraccounts

python manage.py makemigrations admin
python manage.py migrate admin
```

#### Using AbstractBaseUser class
```python
# terminal command
python manage.py startapp users
```


```python
# settings.py

INSTALLED_APPS = [
    ...........
    'users'
]
```

```python
# users/models.py
from django.db import models
from django.contrib.auth.models import AbstractBaseUser, PermissionsMixin, BaseUserManager
from django.utils.translation import gettext_lazy as _


class CustomUserManager(BaseUserManager):

    def create_user(self, email, username, password, **other_fields):
        if not email:
            raise ValueError(_("You must provide an email address."))
        
        email = self.normalize_email(email)
        user = self.model(email=email, username=username, **other_fields)
        user.set_password(password)
        user.save()                          # user.save(using=self._db)
        return user
    
    
    def create_superuser(self, email, username, password, **other_fields):
        other_fields.setdefault("is_staff", True)
        other_fields.setdefault("is_superuser", True)
        other_fields.setdefault("is_active", True)

        if other_fields.get("is_staff") is not True:
            raise ValueError(_("Superuser must have is_staff=True."))
        
        if other_fields.get("is_superuser") is not True:
            raise ValueError(_("Superuser must have is_superuser=True."))
        
        return self.create_user(email, username, password, **other_fields)
    
    

class CustomUser(AbstractBaseUser, PermissionsMixin):

    email        = models.EmailField(_('email address'), unique=True)
    username     = models.CharField(max_length=150, unique=True)
    first_name   = models.CharField(max_length=150, blank=True, null=True)
    last_name    = models.CharField(max_length=150, blank=True, null=True)
    mobile       = models.IntegerField(_('mobile number'), blank=True, null=True)
    about        = models.TextField(_('about'), max_length=500, blank=True, null=True)

    date_joined  = models.DateTimeField(auto_now_add=True)
    last_login   = models.DateTimeField(auto_now=True)

    is_staff     = models.BooleanField(default= False)
    is_superuser = models.BooleanField(default= False) 
    is_active    = models.BooleanField(default= False)

    USERNAME_FIELD =    'email'     
    REQUIRED_FIELDS = ['username'] 

    objects = CustomUserManager()

    class Meta:
        verbose_name_plural = "Custom Users"

    def get_short_name(self):
        return self.email

    def __str__(self):
        return self.username
```

```python
# settings.py

AUTH_USER_MODEL = 'users.CustomUser'
```


```python
# users/forms.py
from django.contrib.auth.forms import UserCreationForm, UserChangeForm
from .models import CustomUser


class CustomUserCreationForm(UserCreationForm):

    class Meta:
        model = CustomUser
        fields = ("email", 'username')


class CustomUserChangeForm(UserChangeForm):

    class Meta:
        model = CustomUser
        fields = ("email",)
```

```python
# users/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin

from .models import CustomUser
from .forms import CustomUserCreationForm, CustomUserChangeForm


class CustomUserAdmin(UserAdmin): 
    form = CustomUserChangeForm
    add_form = CustomUserCreationForm
    model = CustomUser

    list_filter = ('email', 'username', 'is_active', 'is_staff') 
    list_display = ('email', 'username', 'date_joined', 'last_login') 
    ordering = ('-date_joined',)                    
    search_fields = ('email', 'username', 'first_name')  

    fieldsets = (
        (None, {'fields': ('email', 'username', 'first_name', 'last_name', 'password',)}),
        ('Permissions', {'fields': ('is_staff', 'is_active', 'is_superuser')}),
        #('Permissions', {'fields': ('is_staff', 'is_active', 'groups', 'user_permissions')}),
        ('Personal', {'fields': ('about',)}),
    )       

    # fieldsets = UserAdmin.fieldsets + (
    #     ('Additional info', {'fields': ('phone', 'address')}),
    # )

    add_fieldsets = (
        (None, {
            'classes': ('wide',),
            'fields': ('email', 'username', 'password1', 'password2', 'is_staff', 'is_active',)
            }
        ),
    )


admin.site.register(CustomUser, CustomUserAdmin)
```


```python
# users/tests.py
from django.contrib.auth import get_user_model
from django.test import TestCase


class UsersManagersTests(TestCase):

    def test_create_user(self):
        User = get_user_model()
        user = User.objects.create_user(email="normal@user.com", password="foo")
        self.assertEqual(user.email, "normal@user.com")
        self.assertTrue(user.is_active)
        self.assertFalse(user.is_staff)
        self.assertFalse(user.is_superuser)
        try:
            # username is None for the AbstractUser option
            # username does not exist for the AbstractBaseUser option
            self.assertIsNone(user.username)
        except AttributeError:
            pass
        with self.assertRaises(TypeError):
            User.objects.create_user()
        with self.assertRaises(TypeError):
            User.objects.create_user(email="")
        with self.assertRaises(ValueError):
            User.objects.create_user(email="", password="foo")

    def test_create_superuser(self):
        User = get_user_model()
        admin_user = User.objects.create_superuser(email="super@user.com", password="foo")
        self.assertEqual(admin_user.email, "super@user.com")
        self.assertTrue(admin_user.is_active)
        self.assertTrue(admin_user.is_staff)
        self.assertTrue(admin_user.is_superuser)
        try:
            # username is None for the AbstractUser option
            # username does not exist for the AbstractBaseUser option
            self.assertIsNone(admin_user.username)
        except AttributeError:
            pass
        with self.assertRaises(ValueError):
            User.objects.create_superuser(
                email="super@user.com", password="foo", is_superuser=False)
```

[Migrating to a Custom User Model Mid-project in Django](https://testdriven.io/blog/django-custom-user-model-migration/)

```python
python manage.py makemigrations
python manage.py migrate
```

Referencing CustomUser model
```python
# apps/models.py
from django.db import models
from django.conf import settings

class Course(models.Model):
    name = models.CharField(max_length=100)
    tutor = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```

Source:
- [Michael Herman](https://testdriven.io/blog/django-custom-user-model/)
- [Eleanor Stribling](https://medium.com/agatha-codes/options-objects-customizing-the-django-user-model-6d42b3e971a4)


### Authentication

#### Pre-created authentification routes
```python
# urls.py
urlpatterns = [
   .......
   path('', include('django.contrib.auth.urls'))
]
# /login, /lougout, /signup, etc. 
```

```python
# Authentication links
<a href="{% url 'login' %}">Login</a>
<a href="{% url 'signup' %}">Signup</a>
<a href="{% url 'logout' %}">Logout</a>

# Check if user login
{% if request.user.is_authenticated %}
    Logged in as: {{ request.user.username }}
{% endif %}
```


#### Sign up

Class Based View
```python
# Create a Class Based View for Sign up (No default view for sign up)
# SignupView is not pre-created by Djang
# But Sign up view form (UserCreationForm) is pre-created by Django


# views.py
from django.contrib.auth.forms import UserCreationForm
from django.views.generic import CreateView

class SignupView(CreateView):
    template_name = 'registration/signup.html'
    form_class = UserCreationForm

    def get_success_url(self):
        return reverse("login")


# urls.py
from posts.views import SignupView

urlpatterns = [
   .......
   path('signup/', SignupView.as_view(), name='signup')
]
```

Function Based View

```python
# views.py
from django.shortcuts import render, redirect
from django.contrib.auth.forms import UserCreationForm

def signup_view(request):
    form = UserCreationForm(request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('/login')
    return render(request, 'registration/signup.html', {'form': form})
    
# urls.py
from . import views

urlpatterns = [
   .......
   path('signup/', views.signup_view, name='signup_view')
]
```

```html
# signup.html
{% load crispy_forms_tags %}

<form method="post">
    {% csrf_token %}
    {{form|crispy}}                              
    <button type="submit"> Sign Up </button>
</form>
```

Customize the UserCreationForm for Sign up

```python
# forms.py
from django.contrib.auth import get_user_model
from django.contrib.auth.forms import UserCreationForm

User = get_user_model()
class SignUpForm(UserCreationForm):
    class Meta:
        model = User
        fields = ['username']
        fields_classes = {'username': UsernameField}

# or, 

# forms.py
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User

class SignUpForm(UserCreationForm):
    class Meta:
        model = User
        fields = ('username', 'first_name', 'last_name', 'email', )
```

```python        
# views.py
from django.shortcuts import render, redirect
from .forms import SignUpForm

def signup_user(request):
    form = SignUpForm(request.POST or None)
    if request.method == "POST":
        if form.is_valid():
            form.save()
            return redirect('session:login_user')
        
    return render(request, 'session/signup.html', {'form': form})
```

```html
# signup.html
{% load crispy_forms_tags %}

<form method="post" autocomplete="off">
    {% csrf_token %}
    {{form|crispy}}                              
    <button type="submit"> Sign Up </button>
</form>
```

#### Log in
Using default LoginView class. LoginView class is already pre-created by Django.
- https://www.pythontutorial.net/django-tutorial/django-loginview/#

```python
# urls.py
from django.contrib.auth.views import LoginView

urlpatterns = [
   .......
   path('login/', LoginView.as_view(), name='login')
]

# By default, LoginView will try to render registration/login.html
#  and send a login form with it.
# So in our templates folder, create a registration folder 
# and inside that create login.html file.
# if we don't do this, When user click the Login button
# it will cause TemplateDoesNotExist registration/login.html error
```

```html
# login.html

<form method="post">
    {% csrf_token %}
    {{ form }}
    <button type="submit">Login</button>
</form>
```

```python
# When user click the Login button, the LoginView will try to authenticate the user with the form username ans password. 
# After a successful login, we need the redirect to the next page. 
# We can set that next page in settings.py by adding the below code –


# settings.py
LOGIN_REDIRECT_URL = 'app_name:home'

# If we do not set login redirect URL, it will take to default 
# URL /accounts/profile/ which will give TemplateDoesNotExist error



# If we attempt to access a page that requires login, 
# Django will use this LOGIN_URL for redirection. 
# If we don’t add the LOGIN_URL to the settings.py, 
# Django will use the default login URL which is accounts/login/

# settings.py
LOGIN_URL = 'app_name:login'
```

Customize the Login View

```python 
# urls.py
from django.contrib.auth.views import LoginView

urlpatterns = [
   .......
    path('login/', LoginView.as_view(
            redirect_authenticated_user=True, 
            template_name='app_name/login.html'
            ), 
            name='login'
        ),
]

# by setting redirect_authenticated_user parameter is true 
# We can redirect a user who is already authenticated 
```

```html
# app_name/login.html

<form method="post">
    {% csrf_token %}
    {{ form }}
    <button type="submit">Login</button>
</form>
```
```python
# settings.py

LOGIN_REDIRECT_URL = 'app_name:home'
```



Manual Login
```python
# CBV
# https://www.pythontutorial.net/django-tutorial/django-loginview/#
# views.py

from django.contrib.auth.views import LoginView
from django.urls import reverse_lazy
from django.contrib import messages

class MyLoginView(LoginView):
    redirect_authenticated_user = True
    
    def get_success_url(self):
        return reverse_lazy('tasks') 
    
    def form_invalid(self, form):
        messages.error(self.request,'Invalid username or password')
        return self.render_to_response(self.get_context_data(form=form))
        

# urls.py
from django.urls import path
from .views import MyLoginView

urlpatterns = [
    path('login/', MyLoginView.as_view(),name='login'),
]



# login.html
{%extends 'base.html'%}

{%block content%}
  <div class="center">
	  <form method="post" class="card" novalidate>
	  	{% csrf_token %}
	    <h2 class="text-center">Log in to your account</h2>
		{% for field in form %}
	    		{{ field.label_tag }} 
	        	{{ field }}
	        	{% if field.errors %}
	        		<small>{{ field.errors|striptags }}</small> 
	        	{% endif %}
		{% endfor %}
		
		<input type="submit" value="Login" class="btn btn-primary full-width">
		<hr>
		<p class="text-center">Forgot your password <a href="#">Reset Password</a></p>
		<p class="text-center">Don't have a account? <a href="#">Join Now</a></p>
	</form>
</div>

{%endblock content%}
```


```python
# FBV
# views.py
from django.contrib.auth import authenticate, login

def login_page(request):
    if request.method == "POST":
        username = request.POST.get("username")
        password = request.POST.get("password")
        user = authenticate(request, username=username, password=password)
        if user is not None:
            login(request, user)
            return redirect("index")

return render(request, "registration/login.html", {})
```


Login using AuthenticationForm

```python
# views.py
from django.contrib import messages
from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, login
from django.contrib.auth.forms import AuthenticationForm

def login_user(request):
    if request.method == "POST":
        form = AuthenticationForm(request, data=request.POST) 
        # AuthenticationForm has the 'request' as argument
        # this is unique case
        # other model forms don't need this 'request'

        if form.is_valid():
            user = form.get_user()
            # when we use AuthenticationForm
            # we don't need to authenticate user
            # because get_user() method returns user_cache
            # which is authenticated data under 'clean(self)' method
            # click to the get_user() in VScode to explore

            login(request, user)
            return redirect('HomeView')
        
        # Also we don't need to provide any messages.error if form is invalid
        # AuthenticationForm by default shows error messages in 'login.html' if invalid
            
    else:
        form = AuthenticationForm(request)         
    return render(request, 'session/login.html', {'form': form})
```

Login required decorator

```python
# views.py
from django.contrib.auth.decorators import login_required

@login_required(login_url='/login')
def postview(request):
    posts = Post.objects.all()
    return render(request, 'tution/postview.html', {'posts': posts})
```

```html
# login.html
{% load crispy_forms_tags %}

<div>
    <!-- messages from backend -->
    {% if messages %}
        {% for message in messages %}
        <div class="text-center alert alert-{{message.tags}} alert-primary alert-dismissible fade show" role="alert">
            {{message}}
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>
        {% endfor %}
    {% endif %}
            
    <form method="post" autocomplete="off">
        {% csrf_token %}
        {{form|crispy}}                              
    
        <div class="d-grid gap-2">
            <button type="submit" class="btn btn-outline-info mt-2"> Login </button>
        </div> 
    </form>
</div>
```



#### Log out
Using default LogoutView class

```python
# LogoutView is already pre-created by Django

# urls.py
from django.contrib.auth.views import LogoutView

urlpatterns = [
   .......
   path('logout/', LogoutView.as_view(), name='logout')
]


# Include a link in a template
<a> href="{% url 'logout' %}">Logout</a>

# After link is execute, the user will be logout and redirect to LOGOUT_REDIRECT_URL specified in your settings.py 
```
```python
# Logout Redirect URL in settings.py file
# settings.py
LOGOUT_REDIRECT_URL = 'home'


# redirect to a URL after logout, 
# we can set the next page in LogoutView
# urls.py
path('logout/', LogoutView.as_view(
        next_page='home'
        ), 
        name='logout'
    )
    

# redirect to a template after logout, 
# we can set the template name in LogoutView
# urls.py
path('logout/', auth_views.LogoutView.as_view(
        template_name='app_name/logout.html'
        ), 
        name='logout'
    ),
```


Manual Logout

```python
# views.py
from django.shortcuts import redirect
from django.contrib.auth import login

def logout_user(request):
    logout(request)
    return redirect('session:login_user')
```

#### Check if user login
```html
# page.html
{% if request.user.is_authenticated %}
    Logged in as: {{ request.user.username }}
    
    <!-- Capitalize -->
    {{request.user.username.capitalize}}
    {{ request.user.username|upper }}
    {{ request.user.username|capfirst }}
    
{% endif %}

  <form action="{% url 'session:login_user' %}">
    <button class="btn btn-info" type="submit">Log in</button>
  </form> 
  <form action="{% url 'session:signup_user' %}">
    <button class="btn btn-info" type="submit">Sign up</button>
  </form> 
  
{% endif %}
```

### Celery


```python
# Install celery
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

```
# .env

CELERY_BROKER=redis://127.0.0.1:6379/0
CELERY_BACKEND=redis://127.0.0.1:6379/0
```

```python
# settings.py

CELERY_BROKER_URL = os.environ.get('CELERY_BROKER', 'redis://redis:6379/0')
CELERY_RESULT_BACKEND = os.environ.get('CELERY_BACKEND', 'redis://redis:6379/0')
```

```
# redis structure:

redis://<service>:<port>/<redis_database_number_from_0_to_15>

redis://127.0.0.1:6379/0
redis://redis:6379/0

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


### Send Email
```python
EMAIL_USER=example@gmail.com
EMAIL_PASS=examplepass
```

```python
# core/settings.py

### Email setting
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'  # sent email will be appeared in termial

EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_USE_SSL = False
EMAIL_HOST_USER = os.environ.get('EMAIL_USER')
EMAIL_HOST_PASSWORD = os.environ.get('EMAIL_PASS')
```
```python
# file.py
from django.core.mail import send_mail
from django.conf import settings

subject = 'Email subject'
messsage = 'This is message body. Go to the web site to see the detail'
from_email = settings.EMAIL_HOST_USER
recipient_list = ['recipient@gmail.com',]

send_mail(subject, messsage, from_email, recipient_list, fail_silently=False)
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

