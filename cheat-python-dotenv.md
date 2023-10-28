### `.env` file
```python
API_KEY=3asd45hd-gyudkg9083bdh-fgtdmn
EMAIL=admin@example.org
PASSWORD=abcd1234

DOMAIN=example.org
ADMIN_EMAIL=admin@${DOMAIN}       # variables in values, ${variables}, like ${DOMAIN}
ROOT_URL=${DOMAIN}/app
```

### `.gitignore` file
```python
### dotenv ###
.env
```
### `app.py` file
```python
import os
from dotenv import load_dotenv

# take environment variables from .env.
load_dotenv()  
```

```python
print(os.getenv('API_KEY'))
print(os.getenv('PASSWORD'))
print(os.getenv('EMAIL'))
```
```python
print(os.getenv('DOMAIN'))
print(os.getenv('ADMIN_EMAIL'))
print(os.getenv('ROOT_URL'))
```

```python
# all environment variables
print(os.environ)

print(type(os.environ))

# iterating over all environment variables
for var in os.environ.items():
    print(var)
```

```python
# accessing a specific environment variable
api_key = os.environ['API_KEY']
print(f'API key: {api_key}')
```
### override .env variables
```python
# here, environment variables will override .env variables

load_dotenv(override=False)

EMAIL='newemail@example.org'
print(os.getenv('EMAIL')) 
# output: newemail@example.org
```
# .env variables remain unchanged
```python
# here, .env variables will override environment variables

load_dotenv(override=True)     # this is same as load_dotenv()  

EMAIL='newemail@example.org'
print(os.getenv('EMAIL'))
# output: admin@example.org
```
### dotenv_values
```python
from dotenv import dotenv_values

config = dotenv_values(".env")    # it is a dictionary
print(config)                     
print(config['API_KEY'])
```
### load environment variables from other .env files
### `.env.development` file
```python
DOMAIN=example.org
ADMIN_EMAIL=admin@${DOMAIN}       # variables in values, ${variables}, like ${DOMAIN}
ROOT_URL=${DOMAIN}/app
```
### `.env.secret` file
```python
API_KEY=3asd45hd-gyudkg9083bdh-fgtdmn
EMAIL=admin@example.org
PASSWORD=abcd1234
```
### `app2.py` file
```python
import os
from dotenv import dotenv_values, load_dotenv

load_dotenv()
```
```python
config = {
    **dotenv_values('.env.development'),  # load shared development variables
    **dotenv_values('.env.secret'),    # load sensitive variables
    **os.environ,  # override loaded values with environment variables
}

print(config)
```
