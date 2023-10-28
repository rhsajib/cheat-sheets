```zsh
~ % pip install flask
```


```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<p>Hello, World!</p>'
```

### Run flask app
#### Process-1

```zsh
# set environment variables
~ % export FLASK_APP=app.py

# enable debug mode = True
~ % export FLASK_DEBUG=1

# run flask app
~ % flask run
```


#### Process-2
```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<p>Hello,World!</p>'

if __name__ == '__main__':
    app.run(debug=True)
```


```zsh
# run flask app
~ % python app.py
```

```python
# two routes handled by the same function

@app.route('/')
@app.route('/home')
def home():
    return '<p>Hello Page!</p>'
```

### Secret key
```python
# generate secret key using python built in module
>>>import secrets
>>> secrets.token_hex(20)
'5e1f86e8dc3ccb9b8641dcbec666e5d603fdc8aa'
```

```python
# app.py
from flask import Flask, render_template

app = Flask(__name__)
app.config['SECRET_KEY'] = '5e1f86e8dc3ccb9b8641dcbec666e5d603fdc8aa'
```



### Sign up
```zsh
~ % pip install flask-wtf
```

```python
# forms.py
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Length, Email, EqualTo

class RegistrationForm(FlaskForm):
    username = StringField('Username', 
                           render_kw={"placeholder": "Enter your username"},
                           validators=[DataRequired(), Length(min=2, max=20)])
    email = StringField('Email', 
                        validators=[DataRequired(), Email()])
    password1 = PasswordField('Password', validators=[DataRequired()])
    password2 = PasswordField('Confirm Password', 
                              validators=[DataRequired(), EqualTo('password1')])
    submit = SubmitField('Sign Up')
```
```python
# app.py 
from flask import Flask, render_template

@app.route('/register')
def register():
    form = RegistrationForm()
    return render_template('register.html', title = 'Register', form=form)
```
```html
# register.html

{% extends 'base.html' %}
{% block title %}Register{% endblock title %}

{% block content %}
<div class="content-section">
   <form method="POST" action="#">
        {{ form.hidden_tag() }}
        <fieldset class="form-group">
            <legend class="border-bottom mb-4 text-center"> Join Today</legend>
            <div class="form-group mb-3">
                {{ form.username.label(class="form-control-label") }}
                {{ form.username(class="form-control") }}
            </div>
            <div class="form-group mb-3">
                {{ form.email.label(class="form-control-label") }}
                {{ form.email(class="form-control", placeholder="Enter Your Email") }}
            </div>
            <div class="form-group mb-3">
                {{ form.password1.label(class="form-control-label") }}
                {{ form.password1(class="form-control", placeholder="Password") }}
            </div>
            <div class="form-group mb-3">
                {{ form.password2.label(class="form-control-label") }}
                {{ form.password2(class="form-control", placeholder="Confirm Password") }}
            </div>
        </fieldset>
        <div class="form-group">
                <div class="d-grid gap-2 col-6 mx-auto">
                {{ form.submit(class="btn btn-outline-info ") }}
            </div>
        </div>
   </form>
</div>
<div class="border-top pt-3">
    <small class="text-muted">
        Already Have An Account? <a href="{{ url_for('login') }}" class="ml-2">Sign In </a>
    </small>
</div>
{% endblock content %}

```


### Log in
```python
# forms.py
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField, BooleanField
from wtforms.validators import DataRequired, Email


class LoginForm(FlaskForm):
    email = StringField('Email', 
                        validators=[DataRequired(), Email()])
    password = PasswordField('Password', validators=[DataRequired()])
    remember = BooleanField('Remember Me')
    submit = SubmitField('Login')
```

```python
# app.py 
from flask import Flask, render_template

@app.route('/login')
def login():
    form = LoginForm()
    return render_template('login.html', title = 'Login', form=form)
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