
the main app, chat service, and file convert service are using the same Redis server for communication. The Channels layer in Django is configured with a Redis channel layer, and the different services connect to this common layer for communication through WebSocket and Redis Pub/Sub.

This setup allows for a centralized communication channel through which different parts of your application can exchange messages asynchronously. If you have a distributed deployment and want to scale your application horizontally, you can configure multiple Django instances to use the same Redis server, ensuring that all instances share the same communication channel.

If you have specific requirements, such as separating communication channels or using different Redis instances for each service, you can adjust the configuration accordingly. The goal is to provide a flexible and scalable architecture that suits your application's needs.

### Django Backend
- https://reza-ta.medium.com/create-a-publish-subscriber-service-along-side-with-django-ed5337cb2bfb

- https://medium.com/@nandagopal05/scaling-websockets-with-pub-sub-using-python-redis-fastapi-b16392ffe291
- https://betterprogramming.pub/building-a-websocket-server-in-a-microservice-architecture-50c6c6432e2b

- https://betterprogramming.pub/design-considerations-for-scaling-websocket-server-horizontally-with-a-publish-subscribe-pattern-fe6de9988400

-   https://medium.com/@martindegesus1/real-time-progress-bar-using-django-channels-react-and-websockets-7845342418d6

In django channels,
consumers.py == views.py
routing.py == urls.py


```sh
pip install channels
pip install daphne
```

```python
# project directory

app/
    manage.py
    core/
        __init__.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
    chat/
        __init__.py
        consumers.py
        routing.py
```

```python
# core/settings.py

INSTALLED_APPS = [
    'daphne',  # put it first
    ..........
]
```

```python
# chat/consumers.py

import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatRoomConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = f'chat_{self.room_name}'
        print('room_name', self.room_name)
        print('room_group_name', self.room_group_name)

        # Join room group
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )

        await self.accept()

        # Send message to room group
        # await self.channel_layer.group_send(
        #     self.room_group_name,
        #     {
        #         "type": "tester_message",  # remember `tester_message` is a function
        #         "message": 'Hello world'
        #     }
        # )

    # async def tester_message(self, event):
    #     tester = event['message']

    #     # Send message through WebSocket
    #     await self.send(text_data=json.dumps({"tester": tester}))

    async def disconnect(self, close_code):
        # Leave room group
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    # Receive message from WebSocket
    async def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json["message"]
        print(message)

        # Send message to room group
        await self.channel_layer.group_send(
            self.room_group_name, {"type": "chat_message", "message": message}
        )

    async def chat_message(self, event):
        message = event["message"]

        # Send message through WebSocket
        await self.send(text_data=json.dumps({"message": message}))
```

```python
# chat/routing.py

from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r"ws/chat/(?P<room_name>\w+)/$", consumers.ChatRoomConsumer.as_asgi()),
]
```

```python
# core/asgi.py

import os
import chat.routing
from channels.auth import AuthMiddlewareStack
from channels.routing import ProtocolTypeRouter, URLRouter
from django.core.asgi import get_asgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'core.settings')

django_asgi_app = get_asgi_application()

application = ProtocolTypeRouter(
    {
        "http": django_asgi_app,
        'websocket': AuthMiddlewareStack(
            URLRouter(
                chat.routing.websocket_urlpatterns
            )
        )
    }
)

```

```sh
pip install channels_redis
```

```python
# core/settings.py

ASGI_APPLICATION = 'core.asgi.application'

CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("127.0.0.1", 6379)],
        },
    },
}
```

### React Frontend

```js
// websockerEndPoints.js

const WS_BASE_URL = "ws://127.0.0.1:8000";
const client_id = "a4sfdkj490";

export const WS = {
    chat: `${WS_BASE_URL}/ws/chat/${client_id}/`,
};
```

```js
// Chat.jsx
import React, { useEffect, useState } from "react";
import { WS } from "../../services/websockerEndPoints";

const Chat = () => {
    const [messages, setMessages] = useState([]);
    const [messageText, setMessageText] = useState("");
    const [socket, setSocket] = useState(null); // State to store the WebSocket instance

    useEffect(() => {
        console.log(messages);
        // Create a WebSocket instance
        const newSocket = new WebSocket(WS.chat);

        newSocket.onopen = () => {
            console.log("WebSocket connection established.");
        };

        newSocket.onmessage = (e) => {
            const data = JSON.parse(e.data);
            console.log("Received Message:", data.message);
            setMessages([...messages, data.message]);
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
            const data = JSON.stringify({
                message: messageText,
            });
            socket.send(data);
            setMessageText("");
        }
    };

    return (
        <div className="flex flex-col">
            <div className="flex justify-center mt-4 py-2">
                <h1 className="text-3xl">Welcome to chat room</h1>
            </div>

            <div className="flex mt-4 mx-4">
                <div className="w-1/2 flex flex-col justify-center">
                    <form onSubmit={handleFormSubmit}>
                        <input
                            className="border border-blue-300 w-full h-12 mb-4 px-4 py-2 rounded-md"
                            type="text"
                            placeholder="write here..."
                            id="message"
                            value={messageText}
                            onChange={(e) => setMessageText(e.target.value)}
                        />

                        <button
                            type="submit"
                            className="w-1/3 bg-cyan-500 rounded-md py-1 px-2"
                        >
                            Send
                        </button>
                    </form>
                </div>

                <div>
                    {messages.map((msg, idx) => (
                        <p key={idx}>{msg}</p>
                    ))}
                </div>
            </div>
        </div>
    );
};

export default Chat;
```
