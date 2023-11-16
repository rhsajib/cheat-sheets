
## Table of Contents



```zsh
pip install "fastapi[all]"
```


```python
# main.py
from fastapi import FastAPI

app = FastAPI()

@app.get('/')
def root():
    return {'message': 'Hello World'}
```


```zsh
# run the app in uvicorn server
# for 'main.py' file
uvicorn main:app --reload
```

```zsh
# for 'my_app/main.py' file
uvicorn my_app.main:app --reload
```

### Use `pydantic` for data/schema validation
```python
# main.py
from fastapi import FastAPI
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

class Post(BaseModel):
    title: str
    content: str
    created: bool = True
    rating: Optional[int] = None

@app.post('/create')
def create_post(post: Post):
    print(post)

    # convert pydantic model to dictionary
    print(post.model_dump())

    return {'data': post}
```

### Path order
```python
# right order
@app.get('/posts/latest')
def get_post_detail():
    return {'post_detail': 'this is latest post'}

@app.get('/posts/{id}')
def get_post_detail(id: int):
    return {'post_detail': f'post detail of id {id}'}
```

```python
# wrong order
@app.get('/posts/{id}')
def get_post_detail(id: int):
    return {'post_detail': f'post detail of id {id}'}

@app.get('/posts/latest')
def get_post_detail():
    return {'post_detail': 'this is latest post'}
```
### CRUD operations
```python
from typing import Optional
from fastapi import FastAPI, Response, status, HTTPException
from pydantic import BaseModel
from random import randint

app = FastAPI()

my_posts = [
    { 'title': 'post 1', 'content': 'content for post 1', 'id': 1},
    { 'title': 'post 2', 'content': 'content for post 2', 'id': 2},
    { 'title': 'post 3', 'content': 'content for post 3', 'id': 3},
]
```


```python
# create post
class Post(BaseModel):
    title: str
    content: str
    created: bool = True
    rating: Optional[int] = None

@app.post('/create', status_code=status.HTTP_201_CREATED)
def create_post(post: Post):
    post_dict = post.model_dump()
    post_dict['id'] = randint(1,10000)
    my_posts.append(post_dict)
    return {'data': post_dict}
```


```python
# read all post
@app.get('/posts')
def get_posts():
   return {'data': my_posts}

# read single post
def find_post(id):
    for p in my_posts:
        if p.get('id') == id:
            return p
 
@app.get('/posts/{id}')
def get_post_detail(id: int, response: Response):
    post = find_post(id)
    
    if not post:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} was not found')
        # response.status_code = 404
        # response.status_code = status.HTTP_404_NOT_FOUND
        # return {'message': f'Post with id: {id} was not found'}
    return {'post_detail': post}
```


```python
# update post
def find_post_index(id):
    for i, p in enumerate(my_posts):
        if p['id'] == id: 
            return i

@app.put('/posts/{id}')
def create_post(id: int, post: Post):
    index = find_post_index(id)

    if index is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} does not exist!')
    
    post_dict = post.model_dump()
    post_dict['id'] = id
    my_posts[index] = post_dict
    return {'data': post_dict}
```


```python
# delete post
@app.delete('/posts/{id}')
def delete_post(id: int):
    index = find_post_index(id)
    
    if index is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} does not exist!')
    
    my_posts.pop(index)
    return Response(status_code=status.HTTP_204_NO_CONTENT)
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

### Connect PostgreSQL database
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

### Create table in database
```sql
-- create posts table

CREATE TABLE posts (
    id serial PRIMARY KEY,
    title character varying NOT NULL,
    content character varying NOT NULL,
    published boolean DEFAULT true,
    created_at timestamp with time zone DEFAULT now()
);
```


### CRUD operations in postgres database

```python
# create post model
class Post(BaseModel):
    title: str
    content: str
    published: bool = True
```

```python
# create post
@app.post('/create', status_code=status.HTTP_201_CREATED)
def create_post(post: Post):
    cursor.execute("""
                   INSERT INTO posts (title, content, published)
                   VALUES (%s, %s, %s)
                   RETURNING *
                   """, (post.title, post.content, post.published))
    # save the database
    conn.commit() 

    new_post = cursor.fetchone()
    return {'data': new_post}
```

```python
# read all post
@app.get('/posts')
def get_posts():
   cursor.execute("SELECT * FROM posts")
   posts = cursor.fetchall()
   return {'data': posts}


# read single post
@app.get('/posts/{id}')
def get_post_detail(id: int):
    cursor.execute("""SELECT * FROM posts WHERE id = %s""", (str(id),))
    post = cursor.fetchone()

    if not post:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} was not found')
    return {'post_detail': post}
```

```python
# update post  
@app.put('/posts/{id}')
def create_post(id: int, post: Post):
    cursor.execute("""
                   UPDATE posts
                   SET title = %s, content = %s, published = %s
                   WHERE id = %s
                   RETURNING *
                   """, (post.title, post.content, post.published, str(id)))
    
    updated_post = cursor.fetchone()
    conn.commit()

    if updated_post is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} does not exist!')
 
    return {'data': updated_post}
```

```python
# delete post
@app.delete('/posts/{id}', status_code=status.HTTP_204_NO_CONTENT)
def delete_post(id: int):
    cursor.execute("""DELETE FROM posts WHERE id = %s RETURNING *""", (str(id), ))
    deleted_post = cursor.fetchone()

    # save the database
    conn.commit()

    if deleted_post is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} does not exist!')
    
    return {'message': 'post deleted'}
```
```
# path

-app/
    -__init__.py
    -database.py
    -models.py
    -main.py
    -schemas.py
    -utils.py
    -routers/
        -__init__.py
        -post.py
    


```
### SQLAlchemy
```zsh
pip install sqlalchemy
```


```python
# app/database.py

import os
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

from dotenv import load_dotenv
load_dotenv()

# connect with postgres database
HOST = os.environ.get('HOST')
USER = os.environ.get('POSTGRES_USER')
PASSWORD = os.environ.get('POSTGRES_PASSWORD')
DATABASE = os.environ.get('DATABASE')
PORT = int(os.environ.get('PORT'))

SQLALCHEMY_DATABASE_URL = f'postgresql://{USER}:{PASSWORD}@{HOST}/{DATABASE}'

engine = create_engine(SQLALCHEMY_DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()

# Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```


```python
# app/models.py
from sqlalchemy import Boolean, Column, Integer, String
from sqlalchemy.sql.sqltypes import TIMESTAMP
from sqlalchemy.sql.expression import text
from .database import Base

class Post(Base):
    __tablename__ = "posts"

    id = Column(Integer, primary_key=True, nullable=False)
    title = Column(String, nullable=False)
    content = Column(String, nullable=False)
    published = Column(Boolean, server_default='TRUE', nullable=False)
    created_at = Column(TIMESTAMP(timezone=True), 
                        nullable=False, server_default=text('now()'))
```


```python
# app/schemas.py
from pydantic import BaseModel
from datetime import datetime


class PostBase(BaseModel):
    title: str
    content: str
    published: bool = True

class PostCreate(PostBase):
    pass

class PostUpdate(PostBase):
    pass

class Post(PostBase):
    created_at : datetime

    class Config:
        orm_mode = True
```



```python
# app/routers/posts.py

from fastapi import status, HTTPException, Depends, APIRouter
from sqlalchemy.orm import Session
from .. import models, schemas, database



router = APIRouter(
    prefix='/posts',
    tags= ['Posts']         # group the posts in Posts catagory in 127.0.0.1:8000/docs
)


# read all post
@router.get('/', response_model=list[schemas.Post])
def get_posts(db: Session = Depends(database.get_db)):
    posts = db.query(models.Post).all()
    return posts


# read single post
@router.get('/{id}', response_model=schemas.Post)
def get_post_detail(id: int, db: Session = Depends(database.get_db)):
    post = db.query(models.Post).filter(models.Post.id == id).first()

    if not post:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} was not found')
    return post


# create post
@router.post('/create', status_code=status.HTTP_201_CREATED, response_model=schemas.Post)
def create_post(post: schemas.PostCreate, db: Session = Depends(database.get_db)):

    # new_post = models.Post(title=post.title, content=post.content, published=post.published)
    new_post = models.Post(**post.model_dump())

    db.add(new_post)
    db.commit()
    db.refresh(new_post)

    return new_post


# update post  
@router.put('/{id}', response_model=schemas.Post)
def create_post(id: int, updated_post: schemas.PostUpdate,  db: Session = Depends(database.get_db)):
    post_query = db.query(models.Post).filter(models.Post.id == id)
    post = post_query.first()
    if post is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} does not exist!')
   
    post_query.update(updated_post.model_dump(), synchronize_session=False)
    db.commit()

    return post_query.first()


# delete post
@router.delete('/{id}', status_code=status.HTTP_204_NO_CONTENT)
def delete_post(id: int, db: Session = Depends(database.get_db)):
    post = db.query(models.Post).filter(models.Post.id == id)

    if post.first() is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND,
                            detail=f'Post with id: {id} does not exist!')
    
    post.delete(synchronize_session=False)
    db.commit()
    return {'message': 'post deleted'}
```


```python
# app/main.py
from fastapi import FastAPI
from . import models
from .database import engine
from .routers import post, user

models.Base.metadata.create_all(bind=engine)
app = FastAPI()

app.include_router(post.router)


@app.get('/')
def root():
    return {'message': 'Hello world'}
```


### Websocket
`process -1`
```python
# main.py
from fastapi import FastAPI
from app.api.v1.wsocket import websocket_endpoint
from fastapi.routing import APIWebSocketRoute

app = FastAPI()

# Register the WebSocket endpoint
app.router.routes.append(APIWebSocketRoute('/ws/chat/{client_id}', websocket_endpoint))
```
```python
# websocket.py
from fastapi import WebSocket, WebSocketDisconnect

# Store connected WebSocket clients
connected_clients: set[tuple] = set()


async def websocket_endpoint(client_id: str, websocket: WebSocket):
    await websocket.accept()
    print(f"WebSocket connection established for client {client_id}.")

    # Add the WebSocket to the set of connected clients
    connected_clients.add((client_id, websocket))

    print(connected_clients)

    try:
        while True:
            data = await websocket.receive_text()
            print("Message received:", data)

            # Handle incoming messages here
            # ............................
            # ............................
            # ............................

            # Broadcast the message to all connected clients
            for client in connected_clients:
                client_id, client_ws = client
                print('client_ws', client_ws)
                await client_ws.send_text(data)

    except WebSocketDisconnect:
        print("WebSocket connection closed.")

    finally:
        # Remove disconnected client from the set
        print(
            f'Remove disconnected client from the set {client_id}, {websocket}')
        connected_clients.remove((client_id, websocket))
        print(connected_clients)

```



`process -2`
```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from fastapi.routing import APIWebSocketRoute

app = FastAPI()

# Store connected WebSocket clients
connected_clients: set[tuple] = set()

# WebSocket endpoint to handle connections
@app.websocket('/ws/chat/{client_id}')
async def websocket_endpoint(client_id: str, websocket: WebSocket):
    await websocket.accept()
    print(f"WebSocket connection established for client {client_id}.")

    # Add the WebSocket to the set of connected clients
    connected_clients.add((client_id, websocket))

    print(connected_clients)

    try:
        while True:
            data = await websocket.receive_text()
            print("Message received:", data)

            # Handle incoming messages here
            # ............................
            # ............................
            # ............................

            # Broadcast the message to all connected clients
            for client in connected_clients:
                client_id, client_ws = client
                print('client_ws', client_ws)
                await client_ws.send_text(data)

    except WebSocketDisconnect:
        print("WebSocket connection closed.")

    finally:
        # Remove disconnected client from the set
        print(
            f'Remove disconnected client from the set {client_id}, {websocket}')
        connected_clients.remove((client_id, websocket))
        print(connected_clients)
```

```jsx
// App.jsx

import { useEffect, useState } from "react";
import "./App.css";

function App() {
    const [messages, setMessages] = useState([]);
    const [messageText, setMessageText] = useState("");
    const [socket, setSocket] = useState(null); // State to store the WebSocket instance

    const client_id = "a4sfdkj490";
    const url = `ws://127.0.0.1:8000/ws/chat/${client_id}`;

    useEffect(() => {
        // Create a WebSocket instance
        const newSocket = new WebSocket(url);

        newSocket.onopen = () => {
            console.log("WebSocket connection established.");
        };

        newSocket.onmessage = (event) => {
            // console.log("Message received:", event);
            setMessages([...messages, event.data]);
        };

        newSocket.onclose = () => {
            console.log("WebSocket connection closed.");
        };

        setSocket(newSocket);

        return () => {
            // Clean up WebSocket when component unmounts
            console.log("WebSocket cleaned up.");
            newSocket.close();
        };
    }, [messages]);

    const handleFormSubmit = (e) => {
        e.preventDefault(); // Prevent the default form submission behavior

        if (socket) {
            socket.send(messageText);        // https://websockets.readthedocs.io/en/stable/intro/tutorial1.html
            setMessageText("");
        }
    };

    return (
        <div>
            <div className="text-3xl">Chat app</div>
            <form onSubmit={handleFormSubmit}>
                <input
                    className="border border-red-500"
                    placeholder="Write here..."
                    id="message"
                    type="text"
                    value={messageText}
                    onChange={(e) => setMessageText(e.target.value)}
                />
                <button type="text" className="m-3 border py-1 rounded-md px-4">
                    <span>Send</span>
                </button>
                <div>
                    <h1>Messages</h1>
                    {messages.map((message, idx) => (
                        <p key={idx}>{message}</p>
                    ))}
                </div>
            </form>
        </div>
    );
}

export default App;

```




### Background Tasks

```python
from fastapi import BackgroundTasks


def send_email(email, message):
    pass


@app.get("/")
async def ping(background_tasks: BackgroundTasks):
    background_tasks.add_task(send_email, "email@address.com", "Hi!")
    return {"message": "pong!"}
```



### Redis

```sh
# mac os installation
$ brew install redis
```

```zsh
# start the Redis Server
$ redis-server
```

```zsh
# ensure that your Redis server is running
$ redis-cli

# inside redis server shell
$ ping
```

```
broker_url = 'redis://127.0.0.1:6379/0'
result_backend = 'redis://127.0.0.1:6379/0'

broker_url = 'redis://localhost:6379/0'
result_backend = 'redis://localhost:6379/0'
```

### Celery
- https://testdriven.io/blog/fastapi-and-celery/


```sh
$ pip install celery
```

```python
# Project structure

-app/
    -worker/
        -__init__.py
        -celery.py
        -tasks.py
    -main.py

```

```python
# celery.py
from celery import Celery

from app.core.config import settings

celery_app = Celery(
    'app.main',                       # Specify the path to your main module
    include=['app.worker.tasks'],     # Make sure to provide the correct path to your tasks
    broker=settings.CELERY_BROKER_URL,
    backend=settings.CELERY_RESULT_BACKEND,
    broker_connection_retry_on_startup=True
)
```

```python
# tasks.py
from celery import shared_task
import time
from .celery import celery_app


@celery_app.task(name='celery_task')
def celery_task(x:int):
    time.sleep(x)
    print('celery task started.')
    return (4+5)
```

```python
from fastapi import FastAPI
from app.worker.tasks import celery_task
from celery.result import AsyncResult

app = FastAPI()

@app.post("/")
async def first_celery_task(time: int):
    result = celery_task.delay(time)
    # print('result', result, dir(result))
    # Wait for the Celery task to complete and get the result
    result_value = result.get()     # same as      result_value = AsyncResult(result.id).get()

    return {
        "message": "Task triggered",
        "task_id": result.id,
        "result": result_value
        }
```

```sh
# celery server
$ celery -A app.worker.celery worker --loglevel=info 
```

```sh
# fastapi server
$ uvicorn app.main:app --reload  
```










