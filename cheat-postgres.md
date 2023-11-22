### Install Postgresql

```sh
# https://www.postgresql.org/download/macosx/

brew install postgresql@15
```

```sh
postgresql@15 is keg-only, which means it was not symlinked into /usr/local,
because this is an alternate version of another formula.

If you need to have postgresql@15 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/postgresql@15/bin:$PATH"' >> ~/.zshrc

For compilers to find postgresql@15 you may need to set:
  export LDFLAGS="-L/usr/local/opt/postgresql@15/lib"
  export CPPFLAGS="-I/usr/local/opt/postgresql@15/include"

For pkg-config to find postgresql@15 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/postgresql@15/lib/pkgconfig"

To start postgresql@15 now and restart at login:
  brew services start postgresql@15
Or, if you don't want/need a background service you can just run:
  LC_ALL="C" /usr/local/opt/postgresql@15/bin/postgres -D /usr/local/var/postgresql@15
```


### Install psycopg2
```zsh
# installing Psycopg2 in mac

~ % pip install psycopg2-binary


# if you encounter any issue during installation, go through the following steps


# in vs code terminal inside the working virtual environment
# set environment veriables for postgresql
~ % export PG_HOME=/Library/PostgreSQL/15
~ % export PATH=$PATH:$PG_HOME/bin

# verify that the environment variable has been set correctly
echo $PATH

# now install psycopg2
# without setting environment variables psycopg2 installation will causer error
~ % pip install psycopg2
```

```sh
# You can use the netstat command to check if PostgreSQL is listening on its default port (5432):


netstat -an | grep 5432


# If PostgreSQL is running, you will see an entry with LISTEN indicating that it's actively listening on the specified port.

```


### Integrate with django

```sh
# core/.env
DEBUG=True
DATABASE_ENGINE=django.db.backends.postgresql
POSTGRES_DB=
POSTGRES_USER=
POSTGRES_PASSWORD=
DB_HOST=127.0.0.1    
DB_PORT=
SECRET_KEY=
ALGORITHM=
ACCESS_TOKEN_EXPIRE_MINUTES=
```

```python
# coree/config.py
import os
from dotenv import load_dotenv


# Load environment variables from the .env file in the current directory
load_dotenv()
class BaseConfig:
    def __init__(self):
        ...


class DbConfig(BaseConfig):
    DATABASE_ENGINE: str = os.environ.get('DATABASE_ENGINE')
    POSTGRES_DB: str = os.environ.get('POSTGRES_DB')
    POSTGRES_USER: str = os.environ.get('POSTGRES_USER')
    POSTGRES_PASSWORD: str = os.environ.get('POSTGRES_PASSWORD')
    # DB_HOST should be set to the service name defined in your docker-compose.yml
    # PG_IMAGE_HOST is for docker image of postgres
    # DB_HOST: str =  os.environ.get('PG_IMAGE_HOST') if os.environ.get('PG_IMAGE_HOST') else os.environ.get('DB_HOST') 
    DB_HOST: str =  os.environ.get('PG_SERVICE', os.environ.get('DB_HOST'))
    DB_PORT: str = os.environ.get('PORT')

class CoreConfig(DbConfig):
    pass

config = CoreConfig()

```


```python
# coree/settings.py
from core.config import config


# Database
# https://docs.djangoproject.com/en/4.2/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': config.DATABASE_ENGINE,
        'NAME': config.POSTGRES_DB,
        'USER': config.POSTGRES_USER,
        'PASSWORD': config.POSTGRES_PASSWORD,
        'HOST': config.DB_HOST,            # It should be set to the service name defined in your docker-compose.yml
        'PORT': config.DB_PORT,       
    }
}
```

### Connect PostgreSQL database with FastAPI
```zsh
~ %  pip install python-dotenv
```
```python
# .env file
HOST=localhost
DATABASE=database_name
PORT=5432
POSTGRES_USER=username
POSTGRES_PASSWORD=password
```


```python
# main.py
import psycopg2
import os
import time
from psycopg2.extras import RealDictCursor

from dotenv import load_dotenv
load_dotenv()


# connect with postgres database
while True:
    try:
        conn = psycopg2.connect(
            host=os.environ.get('HOST'),
            database=os.environ.get('DATABASE'),
            port=int(os.environ.get('PORT')),
            user=os.environ.get('POSTGRES_USER'),
            password=os.environ.get('POSTGRES_PASSWORD'),
            cursor_factory=RealDictCursor
        )

        cursor = conn.cursor()
        print('Database connection was successful!')
        break
    
    except Exception as error:
        print('Connecting to database failed!!')
        print('Error', error)
        time.sleep(2)

```
