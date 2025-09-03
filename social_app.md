Perfect — thanks for clarifying ✅

You want the **README as a to-do walkthrough *with actual starter code included in each step***. That way, instead of just “do this,” the user will also see the **files and code snippets** they should paste or create.

Here’s a **full zero-knowledge README.md with code base per step** for **Jeco Chat**.

---

# Jeco Chat — Walkthrough To-Do List with Code

Welcome! This is your **step-by-step to-do checklist** to set up **Jeco Chat**, a real-time social networking app built with **React, Django REST Framework, Django Channels, PostgreSQL, and Redis**.

Even if you’ve never coded before, just copy-paste the commands and code exactly as shown.

---

## ✅ Step 1 — Install Prerequisites

Make sure you install:

* **Git** → [Download here](https://git-scm.com/downloads)
* **Python 3.11+** → [Download here](https://www.python.org/downloads/)
* **Node.js (18+) and npm** → [Download here](https://nodejs.org/)
* **PostgreSQL 14+** → [Download here](https://www.postgresql.org/download/)
* **Redis** → required for real-time messaging

Check installation:

```bash
python --version
node -v
npm -v
psql --version
redis-server --version
```

---

## ✅ Step 2 — Get the Project Code

```bash
git clone https://github.com/your-username/jeco-chat.git
cd jeco-chat
```

If no repo exists yet, create it manually:

```bash
mkdir jeco-chat && cd jeco-chat
mkdir backend frontend
```

---

## ✅ Step 3 — Backend Setup (Django + DRF + Channels)

### 3.1 Create the backend project

```bash
cd backend
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install --upgrade pip
pip install django djangorestframework djangorestframework-simplejwt django-cors-headers psycopg[binary] channels channels-redis Pillow python-dotenv
django-admin startproject config .
python manage.py startapp chat
python manage.py startapp accounts
```

---

### 3.2 Configure `settings.py`

Edit `backend/config/settings.py`:

```python
import os
from pathlib import Path
from datetime import timedelta

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.getenv("DJANGO_SECRET_KEY", "dev-key")
DEBUG = True
ALLOWED_HOSTS = ["*"]

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    # Third-party
    "rest_framework",
    "corsheaders",
    "channels",
    # Local
    "chat",
    "accounts",
]

MIDDLEWARE = [
    "corsheaders.middleware.CorsMiddleware",
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]

ROOT_URLCONF = "config.urls"

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.getenv("POSTGRES_DB", "jecochat"),
        "USER": os.getenv("POSTGRES_USER", "postgres"),
        "PASSWORD": os.getenv("POSTGRES_PASSWORD", "postgres"),
        "HOST": os.getenv("POSTGRES_HOST", "localhost"),
        "PORT": os.getenv("POSTGRES_PORT", "5432"),
    }
}

ASGI_APPLICATION = "config.asgi.application"

CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {"hosts": [os.getenv("REDIS_URL", "redis://127.0.0.1:6379/0")]},
    }
}

REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": (
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ),
}

STATIC_URL = "/static/"
CORS_ALLOW_ALL_ORIGINS = True
```

---

### 3.3 Add URLs

`backend/config/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/accounts/", include("accounts.urls")),
    path("api/chat/", include("chat.urls")),
]
```

---

### 3.4 Add ASGI for WebSockets

`backend/config/asgi.py`:

```python
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
import chat.routing

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings")

application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": AuthMiddlewareStack(
        URLRouter(chat.routing.websocket_urlpatterns)
    ),
})
```

---

### 3.5 Create basic Chat app

`backend/chat/models.py`:

```python
from django.db import models
from django.contrib.auth.models import User

class Message(models.Model):
    sender = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.sender.username}: {self.content[:20]}"
```

`backend/chat/consumers.py`:

```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_group_name = "chatroom"
        await self.channel_layer.group_add(self.room_group_name, self.channel_name)
        await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

    async def receive(self, text_data):
        data = json.loads(text_data)
        message = data["message"]

        await self.channel_layer.group_send(
            self.room_group_name, {"type": "chat_message", "message": message}
        )

    async def chat_message(self, event):
        await self.send(text_data=json.dumps({"message": event["message"]}))
```

`backend/chat/routing.py`:

```python
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r"ws/chat/$", consumers.ChatConsumer.as_asgi()),
]
```

`backend/chat/urls.py`:

```python
from django.urls import path
from rest_framework.response import Response
from rest_framework.decorators import api_view

@api_view(["GET"])
def ping(request):
    return Response({"message": "pong"})

urlpatterns = [
    path("ping/", ping),
]
```

---

### 3.6 Run migrations

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
```

Start backend:

```bash
python manage.py runserver
```

---

## ✅ Step 4 — Frontend Setup (React + Bootstrap)

### 4.1 Create project

```bash
cd ../frontend
npm create vite@latest . -- --template react
npm install
npm install bootstrap axios react-router-dom
```

### 4.2 Add Bootstrap

`src/main.jsx`:

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "bootstrap/dist/css/bootstrap.min.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

---

### 4.3 Add a Chat Page

`src/App.jsx`:

```jsx
import { useState, useEffect } from "react";

function App() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const ws = new WebSocket("ws://127.0.0.1:8000/ws/chat/");

  useEffect(() => {
    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data.message]);
    };
  }, []);

  const sendMessage = () => {
    ws.send(JSON.stringify({ message: text }));
    setText("");
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>
      <div className="border p-3 mb-3" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i}>{msg}</div>
        ))}
      </div>
      <input
        value={text}
        onChange={(e) => setText(e.target.value)}
        className="form-control mb-2"
      />
      <button className="btn btn-primary" onClick={sendMessage}>
        Send
      </button>
    </div>
  );
}

export default App;
```

---

### 4.4 Start frontend

```bash
npm run dev
```

Visit: [http://localhost:5173](http://localhost:5173)

Open **two tabs** → send a message → it shows up instantly! 🚀

---

## ✅ Step 5 — Next Steps

* Add **JWT authentication** for users
* Build **profile pages, group chats, comments, sharing**
* Deploy using **Docker Compose (Django + Postgres + Redis + React)**

---

✅ At this point, you have a **working full-stack chat app** with **real-time messaging**!

---

Would you like me to **expand this further to include authentication code (JWT login & register)** so users can actually log in with accounts instead of an open chatroom?

Awesome 🚀 — let’s extend **Jeco Chat** with **user authentication** (JWT-based login & register) so people can **sign up, log in, and then chat under their own accounts** instead of anonymous open chat.

We’ll add the **accounts app** on the backend and integrate it into the React frontend.

Here’s the **walkthrough with code**:

---

# 🔐 Step 6 — Add User Authentication (JWT)

---

## ✅ Backend: Add Accounts App

### 6.1 Update dependencies (already installed if you followed before)

We’re using **djangorestframework-simplejwt** (already in `pip install` earlier).

---

### 6.2 Create serializers

`backend/accounts/serializers.py`:

```python
from rest_framework import serializers
from django.contrib.auth.models import User
from rest_framework_simplejwt.tokens import RefreshToken

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ["id", "username", "email"]

class RegisterSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True)

    class Meta:
        model = User
        fields = ["id", "username", "email", "password"]

    def create(self, validated_data):
        user = User.objects.create_user(
            username=validated_data["username"],
            email=validated_data.get("email"),
            password=validated_data["password"]
        )
        return user

class LoginSerializer(serializers.Serializer):
    username = serializers.CharField()
    password = serializers.CharField(write_only=True)
    token = serializers.CharField(read_only=True)

    def validate(self, data):
        from django.contrib.auth import authenticate
        user = authenticate(username=data["username"], password=data["password"])
        if not user:
            raise serializers.ValidationError("Invalid credentials")
        refresh = RefreshToken.for_user(user)
        return {
            "username": user.username,
            "token": str(refresh.access_token)
        }
```

---

### 6.3 Add views

`backend/accounts/views.py`:

```python
from rest_framework import generics, permissions
from rest_framework.response import Response
from .serializers import RegisterSerializer, LoginSerializer, UserSerializer
from django.contrib.auth.models import User
from rest_framework.decorators import api_view, permission_classes

class RegisterView(generics.CreateAPIView):
    queryset = User.objects.all()
    serializer_class = RegisterSerializer

class LoginView(generics.GenericAPIView):
    serializer_class = LoginSerializer

    def post(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        return Response(serializer.validated_data)

@api_view(["GET"])
@permission_classes([permissions.IsAuthenticated])
def profile(request):
    serializer = UserSerializer(request.user)
    return Response(serializer.data)
```

---

### 6.4 Add URLs

`backend/accounts/urls.py`:

```python
from django.urls import path
from .views import RegisterView, LoginView, profile

urlpatterns = [
    path("register/", RegisterView.as_view(), name="register"),
    path("login/", LoginView.as_view(), name="login"),
    path("profile/", profile, name="profile"),
]
```

---

### 6.5 Update settings

Ensure `settings.py` has JWT authentication:

```python
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": (
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    )
}
```

---

### 6.6 Restart backend

```bash
python manage.py runserver
```

Test with curl or Postman:

```bash
curl -X POST http://127.0.0.1:8000/api/accounts/register/ \
  -H "Content-Type: application/json" \
  -d '{"username":"tester","email":"test@example.com","password":"Pass1234"}'

curl -X POST http://127.0.0.1:8000/api/accounts/login/ \
  -H "Content-Type: application/json" \
  -d '{"username":"tester","password":"Pass1234"}'
```

You should receive a JWT `token`. 🎉

---

## ✅ Frontend: Add Auth Pages

---

### 6.7 Create `AuthService.js`

`frontend/src/services/AuthService.js`:

```jsx
import axios from "axios";

const API_URL = import.meta.env.VITE_API_BASE_URL;

export async function register(username, email, password) {
  const res = await axios.post(`${API_URL}/accounts/register/`, {
    username, email, password
  });
  return res.data;
}

export async function login(username, password) {
  const res = await axios.post(`${API_URL}/accounts/login/`, {
    username, password
  });
  localStorage.setItem("token", res.data.token);
  return res.data;
}

export function logout() {
  localStorage.removeItem("token");
}

export function getToken() {
  return localStorage.getItem("token");
}
```

---

### 6.8 Add Login & Register Pages

`frontend/src/pages/Register.jsx`:

```jsx
import { useState } from "react";
import { register } from "../services/AuthService";

function Register() {
  const [form, setForm] = useState({ username: "", email: "", password: "" });

  const handleChange = (e) => setForm({ ...form, [e.target.name]: e.target.value });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await register(form.username, form.email, form.password);
      alert("Registered! You can now log in.");
    } catch (err) {
      alert("Error: " + err.response.data);
    }
  };

  return (
    <div className="container mt-5">
      <h2>Register</h2>
      <form onSubmit={handleSubmit}>
        <input name="username" placeholder="Username" onChange={handleChange} className="form-control mb-2" />
        <input name="email" placeholder="Email" onChange={handleChange} className="form-control mb-2" />
        <input name="password" type="password" placeholder="Password" onChange={handleChange} className="form-control mb-2" />
        <button className="btn btn-success">Register</button>
      </form>
    </div>
  );
}

export default Register;
```

`frontend/src/pages/Login.jsx`:

```jsx
import { useState } from "react";
import { login } from "../services/AuthService";

function Login() {
  const [form, setForm] = useState({ username: "", password: "" });

  const handleChange = (e) => setForm({ ...form, [e.target.name]: e.target.value });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await login(form.username, form.password);
      alert("Login successful!");
      window.location.href = "/";
    } catch (err) {
      alert("Invalid credentials");
    }
  };

  return (
    <div className="container mt-5">
      <h2>Login</h2>
      <form onSubmit={handleSubmit}>
        <input name="username" placeholder="Username" onChange={handleChange} className="form-control mb-2" />
        <input name="password" type="password" placeholder="Password" onChange={handleChange} className="form-control mb-2" />
        <button className="btn btn-primary">Login</button>
      </form>
    </div>
  );
}

export default Login;
```

---

### 6.9 Update App Routing

`frontend/src/App.jsx`:

```jsx
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";
import Register from "./pages/Register";
import Login from "./pages/Login";
import Chat from "./pages/Chat";

function App() {
  return (
    <Router>
      <nav className="p-3 bg-light">
        <Link to="/" className="me-3">Chat</Link>
        <Link to="/login" className="me-3">Login</Link>
        <Link to="/register">Register</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Chat />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
      </Routes>
    </Router>
  );
}

export default App;
```

---

### 6.10 Update Chat Component to use JWT

Move the chat UI into `frontend/src/pages/Chat.jsx`:

```jsx
import { useState, useEffect } from "react";
import { getToken } from "../services/AuthService";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  let ws;

  useEffect(() => {
    const token = getToken();
    if (!token) {
      alert("You must log in first!");
      window.location.href = "/login";
      return;
    }
    ws = new WebSocket("ws://127.0.0.1:8000/ws/chat/");
    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data.message]);
    };
    return () => ws.close();
  }, []);

  const sendMessage = () => {
    ws.send(JSON.stringify({ message: text }));
    setText("");
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>
      <div className="border p-3 mb-3" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i}>{msg}</div>
        ))}
      </div>
      <input value={text} onChange={(e) => setText(e.target.value)} className="form-control mb-2" />
      <button className="btn btn-primary" onClick={sendMessage}>Send</button>
    </div>
  );
}

export default Chat;
```

---

## 🎉 Test Flow

1. **Register** → [http://localhost:5173/register](http://localhost:5173/register)
2. **Login** → [http://localhost:5173/login](http://localhost:5173/login)
3. **Chat** → [http://localhost:5173/](http://localhost:5173/)

Now, every message is tied to an **authenticated user**.

---

✅ With this, you now have:

* JWT-based **Register / Login**
* Protected **Chat** page
* Real-time messaging with **WebSockets**
* Separate users chatting under their usernames

---

Do you want me to also **extend the backend WebSocket consumer** so that messages are **saved in the database with sender info**, instead of just being broadcast live?

Perfect 🚀 — let’s now **extend the WebSocket backend** so that messages are not just broadcast, but also **saved in the database with the sender’s username**.

This way, when a user sends a message:

* It gets stored in PostgreSQL
* It’s linked to the authenticated user
* Other users receive it instantly in real-time

We’ll also update the **frontend** to display the sender’s username along with the message.

---

# 🔄 Step 7 — Save Chat Messages with Sender Info

---

## ✅ Backend Updates

### 7.1 Update `Message` model

`backend/chat/models.py`:

```python
from django.db import models
from django.contrib.auth.models import User

class Message(models.Model):
    sender = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.sender.username}: {self.content[:20]}"

    def to_dict(self):
        return {
            "id": self.id,
            "sender": self.sender.username,
            "content": self.content,
            "timestamp": self.timestamp.isoformat(),
        }
```

---

### 7.2 Update `ChatConsumer`

We need to authenticate the user who sends a message. For that, we’ll extract the **JWT token** passed from the frontend.

`backend/chat/consumers.py`:

```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from django.contrib.auth.models import AnonymousUser
from django.contrib.auth import get_user_model
from rest_framework_simplejwt.tokens import AccessToken
from .models import Message

User = get_user_model()

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        # Expect token like ws://.../ws/chat/?token=JWT_TOKEN
        self.room_group_name = "chatroom"
        self.user = await self.get_user_from_token(self.scope["query_string"].decode())

        if self.user is None or isinstance(self.user, AnonymousUser):
            await self.close()
        else:
            await self.channel_layer.group_add(self.room_group_name, self.channel_name)
            await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

    async def receive(self, text_data):
        data = json.loads(text_data)
        content = data.get("message")

        if self.user and content:
            message = await self.save_message(self.user, content)

            # Broadcast message to group
            await self.channel_layer.group_send(
                self.room_group_name,
                {"type": "chat_message", "message": message},
            )

    async def chat_message(self, event):
        await self.send(text_data=json.dumps(event["message"]))

    @database_sync_to_async
    def save_message(self, user, content):
        msg = Message.objects.create(sender=user, content=content)
        return msg.to_dict()

    @database_sync_to_async
    def get_user_from_token(self, query_string):
        try:
            params = dict(x.split("=") for x in query_string.split("&"))
            token = params.get("token")
            if not token:
                return None
            access_token = AccessToken(token)
            user = User.objects.get(id=access_token["user_id"])
            return user
        except Exception:
            return None
```

---

### 7.3 Run migrations

Since the `Message` model already existed, no schema change is needed unless you added new fields. Still, run:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## ✅ Frontend Updates

We need to:

* Send JWT token when connecting WebSocket
* Show **username + content** in the chat window

---

### 7.4 Update `Chat.jsx`

`frontend/src/pages/Chat.jsx`:

```jsx
import { useState, useEffect, useRef } from "react";
import { getToken } from "../services/AuthService";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const ws = useRef(null);

  useEffect(() => {
    const token = getToken();
    if (!token) {
      alert("You must log in first!");
      window.location.href = "/login";
      return;
    }

    // Pass token in WebSocket URL
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/?token=${token}`);

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    return () => {
      ws.current.close();
    };
  }, []);

  const sendMessage = () => {
    if (text.trim() && ws.current) {
      ws.current.send(JSON.stringify({ message: text }));
      setText("");
    }
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>
      <div className="border p-3 mb-3" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i}>
            <strong>{msg.sender}:</strong> {msg.content}
          </div>
        ))}
      </div>
      <input
        value={text}
        onChange={(e) => setText(e.target.value)}
        className="form-control mb-2"
        placeholder="Type a message..."
      />
      <button className="btn btn-primary" onClick={sendMessage}>Send</button>
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. **Run backend**

   ```bash
   cd backend
   python manage.py runserver
   ```

2. **Run frontend**

   ```bash
   cd frontend
   npm run dev
   ```

3. **Register two users** → Log them in on separate browser tabs.

4. **Chat** → Each message is saved in DB and displayed with the sender’s username.

Check the DB:

```sql
SELECT * FROM chat_message ORDER BY timestamp DESC;
```

---

## 🎉 Result

Now you have:

* JWT-authenticated users
* Messages stored in PostgreSQL
* Real-time delivery via WebSockets
* Frontend displaying `username: message`

---

Would you like me to **extend this further into Group Chats (rooms)**, so users can join specific chat rooms instead of a single global one?

Awesome 👍 — let’s now upgrade **Jeco Chat** so users can join specific **chat rooms** instead of being stuck in one global chatroom.

This will give us **group chats**, where each room is independent and stores its own messages.

---

# 🔄 Step 8 — Add Group Chats (Rooms)

---

## ✅ Backend Updates

### 8.1 Update Models

We’ll create a `Room` model and link `Message` to a specific room.

`backend/chat/models.py`:

```python
from django.db import models
from django.contrib.auth.models import User

class Room(models.Model):
    name = models.CharField(max_length=100, unique=True)

    def __str__(self):
        return self.name


class Message(models.Model):
    room = models.ForeignKey(Room, on_delete=models.CASCADE, related_name="messages")
    sender = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"[{self.room.name}] {self.sender.username}: {self.content[:20]}"

    def to_dict(self):
        return {
            "id": self.id,
            "room": self.room.name,
            "sender": self.sender.username,
            "content": self.content,
            "timestamp": self.timestamp.isoformat(),
        }
```

---

### 8.2 Run Migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### 8.3 Update `ChatConsumer`

Now each WebSocket connection belongs to a **room**.

`backend/chat/consumers.py`:

```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from django.contrib.auth.models import AnonymousUser
from django.contrib.auth import get_user_model
from rest_framework_simplejwt.tokens import AccessToken
from .models import Message, Room

User = get_user_model()

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        # Extract room name and token from query params
        query = dict(x.split("=") for x in self.scope["query_string"].decode().split("&"))
        self.room_name = query.get("room", "general")  # default = general
        self.token = query.get("token")
        self.user = await self.get_user_from_token(self.token)

        if not self.user or isinstance(self.user, AnonymousUser):
            await self.close()
        else:
            # Ensure room exists
            self.room_group_name = f"chat_{self.room_name}"
            await self.create_room_if_not_exists(self.room_name)

            # Join room
            await self.channel_layer.group_add(self.room_group_name, self.channel_name)
            await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

    async def receive(self, text_data):
        data = json.loads(text_data)
        content = data.get("message")

        if self.user and content:
            message = await self.save_message(self.user, self.room_name, content)

            # Broadcast message to the room
            await self.channel_layer.group_send(
                self.room_group_name,
                {"type": "chat_message", "message": message},
            )

    async def chat_message(self, event):
        await self.send(text_data=json.dumps(event["message"]))

    @database_sync_to_async
    def save_message(self, user, room_name, content):
        room, _ = Room.objects.get_or_create(name=room_name)
        msg = Message.objects.create(sender=user, room=room, content=content)
        return msg.to_dict()

    @database_sync_to_async
    def create_room_if_not_exists(self, room_name):
        Room.objects.get_or_create(name=room_name)

    @database_sync_to_async
    def get_user_from_token(self, token):
        try:
            access_token = AccessToken(token)
            user = User.objects.get(id=access_token["user_id"])
            return user
        except Exception:
            return None
```

---

## ✅ Frontend Updates

### 8.4 Add Room Selection

We’ll let users enter a room name before chatting.

`frontend/src/pages/Chat.jsx`:

```jsx
import { useState, useEffect, useRef } from "react";
import { getToken } from "../services/AuthService";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const [room, setRoom] = useState("general");
  const [joined, setJoined] = useState(false);
  const ws = useRef(null);

  useEffect(() => {
    return () => {
      if (ws.current) ws.current.close();
    };
  }, []);

  const joinRoom = () => {
    const token = getToken();
    if (!token) {
      alert("You must log in first!");
      window.location.href = "/login";
      return;
    }

    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/?token=${token}&room=${room}`);

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setJoined(true);
  };

  const sendMessage = () => {
    if (text.trim() && ws.current) {
      ws.current.send(JSON.stringify({ message: text }));
      setText("");
    }
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>

      {!joined ? (
        <div>
          <input
            value={room}
            onChange={(e) => setRoom(e.target.value)}
            className="form-control mb-2"
            placeholder="Enter room name"
          />
          <button className="btn btn-success" onClick={joinRoom}>
            Join Room
          </button>
        </div>
      ) : (
        <div>
          <h3>Room: {room}</h3>
          <div className="border p-3 mb-3" style={{ height: "300px", overflowY: "scroll" }}>
            {messages.map((msg, i) => (
              <div key={i}>
                <strong>{msg.sender}:</strong> {msg.content}
              </div>
            ))}
          </div>
          <input
            value={text}
            onChange={(e) => setText(e.target.value)}
            className="form-control mb-2"
            placeholder="Type a message..."
          />
          <button className="btn btn-primary" onClick={sendMessage}>
            Send
          </button>
        </div>
      )}
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. **Run backend**

   ```bash
   python manage.py runserver
   ```

2. **Run frontend**

   ```bash
   npm run dev
   ```

3. Open **Tab 1** → Enter room `family` → Chat

4. Open **Tab 2** → Enter room `family` → Messages sync in real-time

5. Open **Tab 3** → Enter room `friends` → Independent chat

---

## 🎉 Result

Now Jeco Chat supports:

* Multiple **chat rooms**
* Each room stores its own history
* Users only see messages from the room they joined

---

👉 Do you want me to extend this next into **loading past messages when someone joins a room** (so they don’t just see new ones)?

Perfect 🚀 — let’s extend **Jeco Chat** so that when a user joins a room, they also **load the past messages** from the database (instead of only seeing new ones).

We’ll add an API endpoint on the backend to fetch old messages and then update the frontend to load them when a room is joined.

---

# 🔄 Step 9 — Load Past Messages on Room Join

---

## ✅ Backend Updates

### 9.1 Create a Serializer

`backend/chat/serializers.py`:

```python
from rest_framework import serializers
from .models import Message

class MessageSerializer(serializers.ModelSerializer):
    sender = serializers.CharField(source="sender.username")
    room = serializers.CharField(source="room.name")

    class Meta:
        model = Message
        fields = ["id", "room", "sender", "content", "timestamp"]
```

---

### 9.2 Create a Messages API View

`backend/chat/views.py`:

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from .models import Room, Message
from .serializers import MessageSerializer

class RoomMessagesView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request, room_name):
        try:
            room = Room.objects.get(name=room_name)
        except Room.DoesNotExist:
            return Response({"error": "Room not found"}, status=404)

        messages = room.messages.order_by("-timestamp")[:50]  # last 50 messages
        serializer = MessageSerializer(messages, many=True)
        return Response(serializer.data)
```

---

### 9.3 Add Route to `urls.py`

`backend/chat/urls.py`:

```python
from django.urls import path
from .views import RoomMessagesView

urlpatterns = [
    path("messages/<str:room_name>/", RoomMessagesView.as_view(), name="room-messages"),
]
```

And include in `backend/urls.py`:

```python
from django.urls import path, include

urlpatterns = [
    path("api/chat/", include("chat.urls")),
]
```

---

## ✅ Frontend Updates

### 9.4 Update `Chat.jsx`

We’ll fetch past messages from the API **before** connecting to WebSocket.

```jsx
import { useState, useEffect, useRef } from "react";
import { getToken } from "../services/AuthService";
import axios from "axios";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const [room, setRoom] = useState("general");
  const [joined, setJoined] = useState(false);
  const ws = useRef(null);

  useEffect(() => {
    return () => {
      if (ws.current) ws.current.close();
    };
  }, []);

  const joinRoom = async () => {
    const token = getToken();
    if (!token) {
      alert("You must log in first!");
      window.location.href = "/login";
      return;
    }

    try {
      // Load past messages from API
      const res = await axios.get(`http://127.0.0.1:8000/api/chat/messages/${room}/`, {
        headers: { Authorization: `Bearer ${token}` },
      });
      // Messages come in reverse order, so flip them
      setMessages(res.data.reverse());
    } catch (err) {
      console.error("Failed to load past messages", err);
    }

    // Now connect WebSocket
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/?token=${token}&room=${room}`);

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setJoined(true);
  };

  const sendMessage = () => {
    if (text.trim() && ws.current) {
      ws.current.send(JSON.stringify({ message: text }));
      setText("");
    }
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>

      {!joined ? (
        <div>
          <input
            value={room}
            onChange={(e) => setRoom(e.target.value)}
            className="form-control mb-2"
            placeholder="Enter room name"
          />
          <button className="btn btn-success" onClick={joinRoom}>
            Join Room
          </button>
        </div>
      ) : (
        <div>
          <h3>Room: {room}</h3>
          <div className="border p-3 mb-3" style={{ height: "300px", overflowY: "scroll" }}>
            {messages.map((msg, i) => (
              <div key={i}>
                <strong>{msg.sender}:</strong> {msg.content}
              </div>
            ))}
          </div>
          <input
            value={text}
            onChange={(e) => setText(e.target.value)}
            className="form-control mb-2"
            placeholder="Type a message..."
          />
          <button className="btn btn-primary" onClick={sendMessage}>
            Send
          </button>
        </div>
      )}
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. Run backend + frontend.
2. Join a room (e.g., `family`).
3. Send some messages.
4. Refresh the page and re-join `family`.

   * The last **50 messages** should load from the database before new ones arrive.

---

## 🎉 Result

Now Jeco Chat supports:

* Multiple chat rooms
* Past message history loaded from DB
* Real-time updates for new messages

---

👉 Do you want me to extend this next into **showing a list of available rooms** (so users don’t have to type the room name manually)?

Perfect — let’s make **room selection easier** by showing users a list of available rooms instead of requiring them to type the name manually.

We’ll add:

* A backend **API endpoint** that lists all existing rooms.
* A frontend **room picker UI** that fetches and displays the available rooms.
* Users can click a room to join it.

---

# 🔄 Step 10 — Show Available Rooms

---

## ✅ Backend Updates

### 10.1 Add Room Serializer

`backend/chat/serializers.py`:

```python
from rest_framework import serializers
from .models import Message, Room

class RoomSerializer(serializers.ModelSerializer):
    class Meta:
        model = Room
        fields = ["id", "name"]
```

---

### 10.2 Add API View to List Rooms

`backend/chat/views.py`:

```python
from rest_framework.generics import ListAPIView
from rest_framework.permissions import IsAuthenticated
from .models import Room
from .serializers import RoomSerializer

class RoomListView(ListAPIView):
    queryset = Room.objects.all()
    serializer_class = RoomSerializer
    permission_classes = [IsAuthenticated]
```

---

### 10.3 Update Routes

`backend/chat/urls.py`:

```python
from django.urls import path
from .views import RoomMessagesView, RoomListView

urlpatterns = [
    path("messages/<str:room_name>/", RoomMessagesView.as_view(), name="room-messages"),
    path("rooms/", RoomListView.as_view(), name="room-list"),
]
```

---

## ✅ Frontend Updates

### 10.4 Update `Chat.jsx`

We’ll:

* Fetch the list of rooms from the API.
* Show them in a clickable list.
* Allow creating/joining a new room manually if desired.

```jsx
import { useState, useEffect, useRef } from "react";
import { getToken } from "../services/AuthService";
import axios from "axios";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const [room, setRoom] = useState("");
  const [rooms, setRooms] = useState([]);
  const [joined, setJoined] = useState(false);
  const ws = useRef(null);

  useEffect(() => {
    const token = getToken();
    if (!token) return;

    // Fetch available rooms
    axios
      .get("http://127.0.0.1:8000/api/chat/rooms/", {
        headers: { Authorization: `Bearer ${token}` },
      })
      .then((res) => setRooms(res.data))
      .catch((err) => console.error("Failed to fetch rooms", err));

    return () => {
      if (ws.current) ws.current.close();
    };
  }, []);

  const joinRoom = async (selectedRoom) => {
    const token = getToken();
    if (!token) {
      alert("You must log in first!");
      window.location.href = "/login";
      return;
    }

    const targetRoom = selectedRoom || room;
    if (!targetRoom) return alert("Enter or select a room");

    try {
      // Load past messages
      const res = await axios.get(
        `http://127.0.0.1:8000/api/chat/messages/${targetRoom}/`,
        { headers: { Authorization: `Bearer ${token}` } }
      );
      setMessages(res.data.reverse());
    } catch (err) {
      console.error("Failed to load past messages", err);
    }

    // Connect WebSocket
    ws.current = new WebSocket(
      `ws://127.0.0.1:8000/ws/chat/?token=${token}&room=${targetRoom}`
    );

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setRoom(targetRoom);
    setJoined(true);
  };

  const sendMessage = () => {
    if (text.trim() && ws.current) {
      ws.current.send(JSON.stringify({ message: text }));
      setText("");
    }
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>

      {!joined ? (
        <div>
          <h3>Available Rooms</h3>
          <ul className="list-group mb-3">
            {rooms.map((r) => (
              <li
                key={r.id}
                className="list-group-item list-group-item-action"
                onClick={() => joinRoom(r.name)}
                style={{ cursor: "pointer" }}
              >
                {r.name}
              </li>
            ))}
          </ul>

          <input
            value={room}
            onChange={(e) => setRoom(e.target.value)}
            className="form-control mb-2"
            placeholder="Or enter a new room"
          />
          <button className="btn btn-success" onClick={() => joinRoom(room)}>
            Join Room
          </button>
        </div>
      ) : (
        <div>
          <h3>Room: {room}</h3>
          <div
            className="border p-3 mb-3"
            style={{ height: "300px", overflowY: "scroll" }}
          >
            {messages.map((msg, i) => (
              <div key={i}>
                <strong>{msg.sender}:</strong> {msg.content}
              </div>
            ))}
          </div>
          <input
            value={text}
            onChange={(e) => setText(e.target.value)}
            className="form-control mb-2"
            placeholder="Type a message..."
          />
          <button className="btn btn-primary" onClick={sendMessage}>
            Send
          </button>
        </div>
      )}
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. Start backend & frontend.
2. Log in.
3. You should see a list of existing rooms (fetched from backend).
4. Click on a room → loads old messages + connects to real-time updates.
5. Optionally enter a **new room name** → it will be created automatically on join.

---

## 🎉 Result

Now Jeco Chat supports:

* Viewing a **list of available rooms**
* Clicking to join any room
* Option to create/join a new room manually
* Still loads past messages + real-time updates

---

👉 Do you want me to extend this next into **direct private messages (1-on-1 chats between users)**, so it’s not just rooms but also personal chats?

Perfect 🔥 — let’s extend **Jeco Chat** to support **direct private messages (1-on-1 chats between users)** in addition to group chat rooms.

We’ll add:

* A **PrivateRoom** model that links two users.
* WebSocket logic to handle direct messages.
* API endpoints so users can see who they can chat with.
* Frontend updates to show a **“Users” list** and allow starting private chats.

---

# 🔄 Step 11 — Add Direct Private Messages

---

## ✅ Backend Updates

### 11.1 Extend Models

`backend/chat/models.py`:

```python
from django.db import models
from django.contrib.auth.models import User

class Room(models.Model):
    name = models.CharField(max_length=100, unique=True)

    def __str__(self):
        return self.name


class PrivateRoom(models.Model):
    user1 = models.ForeignKey(User, on_delete=models.CASCADE, related_name="private_chats_1")
    user2 = models.ForeignKey(User, on_delete=models.CASCADE, related_name="private_chats_2")

    class Meta:
        unique_together = ("user1", "user2")

    def __str__(self):
        return f"PrivateChat: {self.user1.username} <-> {self.user2.username}"

    def get_room_name(self):
        # Deterministic room name for WebSocket groups
        users = sorted([self.user1.username, self.user2.username])
        return f"private_{users[0]}_{users[1]}"


class Message(models.Model):
    room = models.ForeignKey(Room, on_delete=models.CASCADE, related_name="messages", null=True, blank=True)
    private_room = models.ForeignKey(PrivateRoom, on_delete=models.CASCADE, related_name="messages", null=True, blank=True)
    sender = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        if self.room:
            return f"[{self.room.name}] {self.sender.username}: {self.content[:20]}"
        if self.private_room:
            return f"[DM] {self.sender.username}: {self.content[:20]}"
        return f"{self.sender.username}: {self.content[:20]}"

    def to_dict(self):
        return {
            "id": self.id,
            "room": self.room.name if self.room else None,
            "private": self.private_room.get_room_name() if self.private_room else None,
            "sender": self.sender.username,
            "content": self.content,
            "timestamp": self.timestamp.isoformat(),
        }
```

---

### 11.2 Update Consumer for Private Messages

`backend/chat/consumers.py`:

```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from django.contrib.auth.models import AnonymousUser
from django.contrib.auth import get_user_model
from rest_framework_simplejwt.tokens import AccessToken
from .models import Message, Room, PrivateRoom

User = get_user_model()

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        query = dict(x.split("=") for x in self.scope["query_string"].decode().split("&"))
        self.token = query.get("token")
        self.room_name = query.get("room")
        self.private_with = query.get("user")  # For private DM
        self.user = await self.get_user_from_token(self.token)

        if not self.user or isinstance(self.user, AnonymousUser):
            await self.close()
            return

        if self.private_with:
            # Setup private chat
            self.room_group_name = await self.get_private_room_name(self.user.username, self.private_with)
        else:
            # Normal group chat
            self.room_group_name = f"chat_{self.room_name}"
            await self.create_room_if_not_exists(self.room_name)

        await self.channel_layer.group_add(self.room_group_name, self.channel_name)
        await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

    async def receive(self, text_data):
        data = json.loads(text_data)
        content = data.get("message")

        if self.user and content:
            if self.private_with:
                message = await self.save_private_message(self.user.username, self.private_with, content)
            else:
                message = await self.save_group_message(self.user, self.room_name, content)

            await self.channel_layer.group_send(
                self.room_group_name,
                {"type": "chat_message", "message": message},
            )

    async def chat_message(self, event):
        await self.send(text_data=json.dumps(event["message"]))

    @database_sync_to_async
    def save_group_message(self, user, room_name, content):
        room, _ = Room.objects.get_or_create(name=room_name)
        msg = Message.objects.create(sender=user, room=room, content=content)
        return msg.to_dict()

    @database_sync_to_async
    def save_private_message(self, sender_username, receiver_username, content):
        sender = User.objects.get(username=sender_username)
        receiver = User.objects.get(username=receiver_username)

        room, _ = PrivateRoom.objects.get_or_create(
            user1=min(sender, receiver, key=lambda u: u.id),
            user2=max(sender, receiver, key=lambda u: u.id),
        )
        msg = Message.objects.create(sender=sender, private_room=room, content=content)
        return msg.to_dict()

    @database_sync_to_async
    def create_room_if_not_exists(self, room_name):
        Room.objects.get_or_create(name=room_name)

    @database_sync_to_async
    def get_private_room_name(self, user1, user2):
        users = sorted([user1, user2])
        return f"private_{users[0]}_{users[1]}"

    @database_sync_to_async
    def get_user_from_token(self, token):
        try:
            access_token = AccessToken(token)
            user = User.objects.get(id=access_token["user_id"])
            return user
        except Exception:
            return None
```

---

### 11.3 Add User List API

`backend/chat/views.py`:

```python
from django.contrib.auth.models import User
from rest_framework.generics import ListAPIView
from rest_framework.permissions import IsAuthenticated
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ["id", "username"]

class UserListView(ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAuthenticated]
```

---

### 11.4 Add Route

`backend/chat/urls.py`:

```python
from django.urls import path
from .views import RoomMessagesView, RoomListView, UserListView

urlpatterns = [
    path("messages/<str:room_name>/", RoomMessagesView.as_view(), name="room-messages"),
    path("rooms/", RoomListView.as_view(), name="room-list"),
    path("users/", UserListView.as_view(), name="user-list"),
]
```

---

## ✅ Frontend Updates

### 11.5 Update `Chat.jsx`

Now we’ll show a **Users list** alongside Rooms. Clicking a username starts a private chat.

```jsx
import { useState, useEffect, useRef } from "react";
import { getToken } from "../services/AuthService";
import axios from "axios";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const [room, setRoom] = useState("");
  const [rooms, setRooms] = useState([]);
  const [users, setUsers] = useState([]);
  const [joined, setJoined] = useState(false);
  const [chatType, setChatType] = useState(null); // "room" or "private"
  const ws = useRef(null);

  useEffect(() => {
    const token = getToken();
    if (!token) return;

    // Fetch rooms
    axios
      .get("http://127.0.0.1:8000/api/chat/rooms/", {
        headers: { Authorization: `Bearer ${token}` },
      })
      .then((res) => setRooms(res.data));

    // Fetch users
    axios
      .get("http://127.0.0.1:8000/api/chat/users/", {
        headers: { Authorization: `Bearer ${token}` },
      })
      .then((res) => setUsers(res.data));

    return () => {
      if (ws.current) ws.current.close();
    };
  }, []);

  const joinRoom = async (targetRoom) => {
    const token = getToken();
    setChatType("room");

    ws.current = new WebSocket(
      `ws://127.0.0.1:8000/ws/chat/?token=${token}&room=${targetRoom}`
    );

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setRoom(targetRoom);
    setJoined(true);
  };

  const startPrivateChat = async (username) => {
    const token = getToken();
    setChatType("private");

    ws.current = new WebSocket(
      `ws://127.0.0.1:8000/ws/chat/?token=${token}&user=${username}`
    );

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setRoom(`Private with ${username}`);
    setJoined(true);
  };

  const sendMessage = () => {
    if (text.trim() && ws.current) {
      ws.current.send(JSON.stringify({ message: text }));
      setText("");
    }
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>

      {!joined ? (
        <div className="row">
          <div className="col-md-6">
            <h3>Rooms</h3>
            <ul className="list-group mb-3">
              {rooms.map((r) => (
                <li
                  key={r.id}
                  className="list-group-item list-group-item-action"
                  onClick={() => joinRoom(r.name)}
                  style={{ cursor: "pointer" }}
                >
                  {r.name}
                </li>
              ))}
            </ul>
          </div>

          <div className="col-md-6">
            <h3>Users</h3>
            <ul className="list-group mb-3">
              {users.map((u) => (
                <li
                  key={u.id}
                  className="list-group-item list-group-item-action"
                  onClick={() => startPrivateChat(u.username)}
                  style={{ cursor: "pointer" }}
                >
                  {u.username}
                </li>
              ))}
            </ul>
          </div>
        </div>
      ) : (
        <div>
          <h3>{room}</h3>
          <div
            className="border p-3 mb-3"
            style={{ height: "300px", overflowY: "scroll" }}
          >
            {messages.map((msg, i) => (
              <div key={i}>
                <strong>{msg.sender}:</strong> {msg.content}
              </div>
            ))}
          </div>
          <input
            value={text}
            onChange={(e) => setText(e.target.value)}
            className="form-control mb-2"
            placeholder="Type a message..."
          />
          <button className="btn btn-primary" onClick={sendMessage}>
            Send
          </button>
        </div>
      )}
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. Log in with **User A** in one browser, **User B** in another.
2. Click on **Rooms** → join a public room (works as before).
3. Click on **User B** from **User A’s list** → starts private chat.
4. Both users should see direct messages in real-time, separate from rooms.

---

## 🎉 Result

Now Jeco Chat supports:

* Group rooms
* Private 1-on-1 chats
* Room + User lists in the UI
* Separate real-time streams for public & private messages

---

👉 Do you want me to extend this next into **notifications (e.g., new message alerts when you’re not in that room/private chat)**?

Perfect 🔔 — let’s add **real-time notifications** so users see an alert when they get a **new message in a room or private chat they’re not currently viewing**.

This will require:

* Backend: sending a **notification event** alongside the message.
* Frontend: a **notification panel** (like a badge or list of unread chats).
* Tracking unread messages by room or private chat.

---

# 🔄 Step 12 — Add Notifications

---

## ✅ Backend Updates

We’ll expand our **ChatConsumer** so every message sent also triggers a **notification event** for the target user(s).

### 12.1 Add Notification Model

`backend/chat/models.py`:

```python
class Notification(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="notifications")
    message = models.ForeignKey(Message, on_delete=models.CASCADE, related_name="notifications")
    is_read = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"Notify {self.user.username} -> {self.message.content[:20]}"
```

---

### 12.2 Update Consumer to Create Notifications

`backend/chat/consumers.py`:

```python
    @database_sync_to_async
    def save_private_message(self, sender_username, receiver_username, content):
        sender = User.objects.get(username=sender_username)
        receiver = User.objects.get(username=receiver_username)

        room, _ = PrivateRoom.objects.get_or_create(
            user1=min(sender, receiver, key=lambda u: u.id),
            user2=max(sender, receiver, key=lambda u: u.id),
        )
        msg = Message.objects.create(sender=sender, private_room=room, content=content)

        # Create notification for receiver
        Notification.objects.create(user=receiver, message=msg)

        return msg.to_dict()

    @database_sync_to_async
    def save_group_message(self, user, room_name, content):
        room, _ = Room.objects.get_or_create(name=room_name)
        msg = Message.objects.create(sender=user, room=room, content=content)

        # Notify all other users (who are members of this room in future)
        # For now: notify everyone except sender
        for u in User.objects.exclude(id=user.id):
            Notification.objects.create(user=u, message=msg)

        return msg.to_dict()
```

---

### 12.3 Notifications API

`backend/chat/views.py`:

```python
from .models import Notification
from rest_framework.generics import ListAPIView, UpdateAPIView
from rest_framework.permissions import IsAuthenticated

class NotificationSerializer(serializers.ModelSerializer):
    message = serializers.SerializerMethodField()

    class Meta:
        model = Notification
        fields = ["id", "message", "is_read", "created_at"]

    def get_message(self, obj):
        return obj.message.to_dict()

class NotificationListView(ListAPIView):
    serializer_class = NotificationSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        return Notification.objects.filter(user=self.request.user, is_read=False).order_by("-created_at")

class NotificationMarkReadView(UpdateAPIView):
    serializer_class = NotificationSerializer
    permission_classes = [IsAuthenticated]
    queryset = Notification.objects.all()

    def perform_update(self, serializer):
        serializer.save(is_read=True)
```

---

### 12.4 Add Routes

`backend/chat/urls.py`:

```python
from .views import RoomMessagesView, RoomListView, UserListView, NotificationListView, NotificationMarkReadView

urlpatterns = [
    path("messages/<str:room_name>/", RoomMessagesView.as_view(), name="room-messages"),
    path("rooms/", RoomListView.as_view(), name="room-list"),
    path("users/", UserListView.as_view(), name="user-list"),
    path("notifications/", NotificationListView.as_view(), name="notification-list"),
    path("notifications/<int:pk>/read/", NotificationMarkReadView.as_view(), name="notification-mark-read"),
]
```

---

## ✅ Frontend Updates

### 12.5 Update `Chat.jsx` with Notifications

We’ll fetch **notifications** every few seconds and display a badge.

```jsx
import { useState, useEffect, useRef } from "react";
import { getToken } from "../services/AuthService";
import axios from "axios";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [text, setText] = useState("");
  const [room, setRoom] = useState("");
  const [rooms, setRooms] = useState([]);
  const [users, setUsers] = useState([]);
  const [notifications, setNotifications] = useState([]);
  const [joined, setJoined] = useState(false);
  const [chatType, setChatType] = useState(null);
  const ws = useRef(null);

  const token = getToken();

  // Fetch rooms & users
  useEffect(() => {
    if (!token) return;

    axios
      .get("http://127.0.0.1:8000/api/chat/rooms/", {
        headers: { Authorization: `Bearer ${token}` },
      })
      .then((res) => setRooms(res.data));

    axios
      .get("http://127.0.0.1:8000/api/chat/users/", {
        headers: { Authorization: `Bearer ${token}` },
      })
      .then((res) => setUsers(res.data));

    return () => {
      if (ws.current) ws.current.close();
    };
  }, [token]);

  // Fetch notifications every 5s
  useEffect(() => {
    if (!token) return;
    const fetchNotifications = () => {
      axios
        .get("http://127.0.0.1:8000/api/chat/notifications/", {
          headers: { Authorization: `Bearer ${token}` },
        })
        .then((res) => setNotifications(res.data));
    };

    fetchNotifications();
    const interval = setInterval(fetchNotifications, 5000);
    return () => clearInterval(interval);
  }, [token]);

  const joinRoom = async (targetRoom) => {
    ws.current = new WebSocket(
      `ws://127.0.0.1:8000/ws/chat/?token=${token}&room=${targetRoom}`
    );

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setRoom(targetRoom);
    setChatType("room");
    setJoined(true);

    // Clear related notifications
    clearNotificationsFor(targetRoom, "room");
  };

  const startPrivateChat = async (username) => {
    ws.current = new WebSocket(
      `ws://127.0.0.1:8000/ws/chat/?token=${token}&user=${username}`
    );

    ws.current.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    setRoom(`Private with ${username}`);
    setChatType("private");
    setJoined(true);

    // Clear related notifications
    clearNotificationsFor(username, "private");
  };

  const sendMessage = () => {
    if (text.trim() && ws.current) {
      ws.current.send(JSON.stringify({ message: text }));
      setText("");
    }
  };

  const clearNotificationsFor = async (target, type) => {
    const toClear = notifications.filter((n) => {
      if (type === "room" && n.message.room === target) return true;
      if (type === "private" && n.message.private?.includes(target)) return true;
      return false;
    });

    for (let n of toClear) {
      await axios.patch(
        `http://127.0.0.1:8000/api/chat/notifications/${n.id}/read/`,
        { is_read: true },
        { headers: { Authorization: `Bearer ${token}` } }
      );
    }
  };

  return (
    <div className="container mt-5">
      <h1>💬 Jeco Chat</h1>

      <div className="mb-3">
        <h4>
          🔔 Notifications ({notifications.length})
        </h4>
        {notifications.map((n) => (
          <div key={n.id} className="alert alert-info p-2">
            <strong>{n.message.sender}</strong>: {n.message.content}
          </div>
        ))}
      </div>

      {!joined ? (
        <div className="row">
          <div className="col-md-6">
            <h3>Rooms</h3>
            <ul className="list-group mb-3">
              {rooms.map((r) => (
                <li
                  key={r.id}
                  className="list-group-item d-flex justify-content-between align-items-center"
                  onClick={() => joinRoom(r.name)}
                  style={{ cursor: "pointer" }}
                >
                  {r.name}
                  <span className="badge bg-danger">
                    {
                      notifications.filter((n) => n.message.room === r.name)
                        .length
                    }
                  </span>
                </li>
              ))}
            </ul>
          </div>

          <div className="col-md-6">
            <h3>Users</h3>
            <ul className="list-group mb-3">
              {users.map((u) => (
                <li
                  key={u.id}
                  className="list-group-item d-flex justify-content-between align-items-center"
                  onClick={() => startPrivateChat(u.username)}
                  style={{ cursor: "pointer" }}
                >
                  {u.username}
                  <span className="badge bg-danger">
                    {
                      notifications.filter(
                        (n) => n.message.private?.includes(u.username)
                      ).length
                    }
                  </span>
                </li>
              ))}
            </ul>
          </div>
        </div>
      ) : (
        <div>
          <h3>{room}</h3>
          <div
            className="border p-3 mb-3"
            style={{ height: "300px", overflowY: "scroll" }}
          >
            {messages.map((msg, i) => (
              <div key={i}>
                <strong>{msg.sender}:</strong> {msg.content}
              </div>
            ))}
          </div>
          <input
            value={text}
            onChange={(e) => setText(e.target.value)}
            className="form-control mb-2"
            placeholder="Type a message..."
          />
          <button className="btn btn-primary" onClick={sendMessage}>
            Send
          </button>
        </div>
      )}
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. Open **User A** in Chrome, **User B** in Firefox.
2. **User B** sends a message in a room that User A hasn’t joined yet → User A sees a 🔔 notification.
3. **User B** sends a private DM → User A sees a notification badge beside User B’s name.
4. When User A clicks the room or User → notifications clear automatically.

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ Group rooms
* ✅ Private DMs
* ✅ Real-time notifications with unread badges

---

👉 Do you want me to take this further into **“online status (who’s active right now)”** so users know who’s available before starting a chat?

Awesome 🚀 — let’s add **real-time online status (presence)** so users can see who’s online before starting a chat.

We’ll:

* Update the **WebSocket consumer** to track connected users.
* Broadcast when a user **connects** or **disconnects**.
* Show online/offline status in the **frontend user list**.

---

# 🔄 Step 13 — Add Online Status (Presence)

---

## ✅ Backend Updates

### 13.1 Extend `ChatConsumer` to Track Active Users

We’ll keep a global in-memory store (for dev) of connected users.
Later, this can move to **Redis** for production scaling.

`backend/chat/consumers.py`:

```python
active_users = set()

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        user = self.scope["user"]
        if user.is_authenticated:
            active_users.add(user.username)
            await self.channel_layer.group_add("presence", self.channel_name)
            await self.channel_layer.group_send(
                "presence",
                {
                    "type": "user_status",
                    "user": user.username,
                    "status": "online",
                },
            )
        await self.accept()

    async def disconnect(self, close_code):
        user = self.scope["user"]
        if user.is_authenticated and user.username in active_users:
            active_users.remove(user.username)
            await self.channel_layer.group_send(
                "presence",
                {
                    "type": "user_status",
                    "user": user.username,
                    "status": "offline",
                },
            )

    async def receive(self, text_data):
        # (existing message handling stays the same)
        data = json.loads(text_data)
        message = data.get("message")

        if "room" in self.scope["url_route"]["kwargs"]:
            room_name = self.scope["url_route"]["kwargs"]["room"]
            msg = await self.save_group_message(self.scope["user"], room_name, message)
            await self.channel_layer.group_send(
                room_name,
                {"type": "chat_message", **msg},
            )
        elif "user" in self.scope["url_route"]["kwargs"]:
            receiver = self.scope["url_route"]["kwargs"]["user"]
            msg = await self.save_private_message(self.scope["user"].username, receiver, message)
            await self.channel_layer.group_send(
                f"private_{receiver}",
                {"type": "chat_message", **msg},
            )
            await self.channel_layer.group_add(f"private_{self.scope['user'].username}", self.channel_name)

    async def chat_message(self, event):
        await self.send(text_data=json.dumps(event))

    async def user_status(self, event):
        # Broadcast presence updates
        await self.send(text_data=json.dumps({
            "type": "status",
            "user": event["user"],
            "status": event["status"],
        }))
```

---

### 13.2 Add API for Initial Online Users

`backend/chat/views.py`:

```python
from rest_framework.response import Response
from rest_framework.views import APIView

class OnlineUsersView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response(list(active_users))
```

---

### 13.3 Add Route

`backend/chat/urls.py`:

```python
from .views import OnlineUsersView

urlpatterns += [
    path("online-users/", OnlineUsersView.as_view(), name="online-users"),
]
```

---

## ✅ Frontend Updates

### 13.4 Connect to Presence Channel

We’ll use a **dedicated WebSocket** for presence updates.

`Chat.jsx`:

```jsx
const [onlineUsers, setOnlineUsers] = useState([]);

useEffect(() => {
  if (!token) return;

  // Get initial online users
  axios
    .get("http://127.0.0.1:8000/api/chat/online-users/", {
      headers: { Authorization: `Bearer ${token}` },
    })
    .then((res) => setOnlineUsers(res.data));

  // Connect to presence WebSocket
  const presenceSocket = new WebSocket(
    `ws://127.0.0.1:8000/ws/chat/?token=${token}`
  );

  presenceSocket.onmessage = (event) => {
    const data = JSON.parse(event.data);
    if (data.type === "status") {
      setOnlineUsers((prev) => {
        if (data.status === "online") {
          return [...new Set([...prev, data.user])];
        } else {
          return prev.filter((u) => u !== data.user);
        }
      });
    }
  };

  return () => presenceSocket.close();
}, [token]);
```

---

### 13.5 Show Status in User List

Update the **users list rendering**:

```jsx
<ul className="list-group mb-3">
  {users.map((u) => (
    <li
      key={u.id}
      className="list-group-item d-flex justify-content-between align-items-center"
      onClick={() => startPrivateChat(u.username)}
      style={{ cursor: "pointer" }}
    >
      {u.username}
      <span
        className={`badge ${
          onlineUsers.includes(u.username) ? "bg-success" : "bg-secondary"
        }`}
      >
        {onlineUsers.includes(u.username) ? "Online" : "Offline"}
      </span>
    </li>
  ))}
</ul>
```

---

## ✅ Test Flow

1. Open **User A** in Chrome, **User B** in Firefox.
2. Log both in → each sees the other as **Online**.
3. Close User B’s browser → User A sees them switch to **Offline**.
4. When User B logs back in → User A sees **Online** again in real time.

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ Real-time presence tracking
* ✅ Online/offline badges next to users
* ✅ Status updates broadcast instantly

---

👉 Next level: Do you want me to extend this into a **"typing indicator" (e.g. “User is typing…")** so users see when the other person is actively writing a message?

Great choice 😃 — let’s add a **typing indicator** so users can see when someone is writing in a room or private chat.

We’ll:

* Extend the **WebSocket consumer** to handle a `"typing"` event.
* Broadcast typing events to all users in that chat (room or private).
* Update the frontend to display `"User is typing..."` below the chat box.

---

# 🔄 Step 14 — Add Typing Indicator

---

## ✅ Backend Updates

### 14.1 Update `ChatConsumer`

We’ll add a handler for `"typing"` events.

`backend/chat/consumers.py`:

```python
async def receive(self, text_data):
    data = json.loads(text_data)

    if "typing" in data:  
        # typing event (not a message)
        if "room" in self.scope["url_route"]["kwargs"]:
            room_name = self.scope["url_route"]["kwargs"]["room"]
            await self.channel_layer.group_send(
                room_name,
                {
                    "type": "user_typing",
                    "user": self.scope["user"].username,
                    "typing": data["typing"],
                },
            )
        elif "user" in self.scope["url_route"]["kwargs"]:
            receiver = self.scope["url_route"]["kwargs"]["user"]
            await self.channel_layer.group_send(
                f"private_{receiver}",
                {
                    "type": "user_typing",
                    "user": self.scope["user"].username,
                    "typing": data["typing"],
                },
            )
    elif "message" in data:  
        # existing message handling
        message = data.get("message")

        if "room" in self.scope["url_route"]["kwargs"]:
            room_name = self.scope["url_route"]["kwargs"]["room"]
            msg = await self.save_group_message(self.scope["user"], room_name, message)
            await self.channel_layer.group_send(
                room_name,
                {"type": "chat_message", **msg},
            )
        elif "user" in self.scope["url_route"]["kwargs"]:
            receiver = self.scope["url_route"]["kwargs"]["user"]
            msg = await self.save_private_message(self.scope["user"].username, receiver, message)
            await self.channel_layer.group_send(
                f"private_{receiver}",
                {"type": "chat_message", **msg},
            )
            await self.channel_layer.group_add(f"private_{self.scope['user'].username}", self.channel_name)

async def user_typing(self, event):
    await self.send(text_data=json.dumps({
        "type": "typing",
        "user": event["user"],
        "typing": event["typing"],
    }))
```

---

## ✅ Frontend Updates

### 14.2 Track Typing State

We’ll send `"typing": true/false` events when a user starts/stops typing.

In `Chat.jsx`:

```jsx
const [typingUsers, setTypingUsers] = useState([]);

// Handle typing events
useEffect(() => {
  if (!ws.current) return;
  ws.current.onmessage = (event) => {
    const data = JSON.parse(event.data);

    if (data.type === "chat_message") {
      setMessages((prev) => [...prev, data]);
    } else if (data.type === "typing") {
      if (data.typing) {
        setTypingUsers((prev) => [...new Set([...prev, data.user])]);
      } else {
        setTypingUsers((prev) => prev.filter((u) => u !== data.user));
      }
    }
  };
}, [ws.current]);

// Send typing status
const handleTyping = (e) => {
  setText(e.target.value);
  if (ws.current) {
    ws.current.send(JSON.stringify({ typing: e.target.value.length > 0 }));
  }
};
```

---

### 14.3 Show Typing Indicator

Update the chat UI to display typing users:

```jsx
<div
  className="border p-3 mb-3"
  style={{ height: "300px", overflowY: "scroll" }}
>
  {messages.map((msg, i) => (
    <div key={i}>
      <strong>{msg.sender}:</strong> {msg.content}
    </div>
  ))}
</div>

{typingUsers.length > 0 && (
  <div className="text-muted mb-2">
    {typingUsers.join(", ")} {typingUsers.length === 1 ? "is" : "are"} typing...
  </div>
)}

<input
  value={text}
  onChange={handleTyping}
  className="form-control mb-2"
  placeholder="Type a message..."
/>
<button className="btn btn-primary" onClick={sendMessage}>
  Send
</button>
```

---

## ✅ Test Flow

1. Open **User A** and **User B** in two browsers.
2. User A starts typing → User B sees **“UserA is typing…”**.
3. When User A stops typing (input cleared or message sent), the indicator disappears.
4. Works in both **rooms** and **private chats**.

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ Typing indicators in real-time
* ✅ Works for rooms and private chats
* ✅ Shows multiple users typing at once

---

👉 Next upgrade: Do you want me to add **message reactions (👍❤️😂 etc.)** so users can react to messages without sending a reply?


Perfect 🎉 — let’s add **message reactions (👍❤️😂 etc.)** so users can quickly respond without typing a reply.

We’ll:

* Extend the **Message model** with a `Reaction` model.
* Add an **API + WebSocket event** for reacting to messages.
* Show reactions under each message in the chat UI.

---

# 🔄 Step 15 — Add Message Reactions

---

## ✅ Backend Updates

### 15.1 Create a `Reaction` Model

`backend/chat/models.py`:

```python
class Reaction(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="reactions")
    message = models.ForeignKey(Message, on_delete=models.CASCADE, related_name="reactions")
    emoji = models.CharField(max_length=10)  # e.g. 👍 ❤️ 😂
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        unique_together = ("user", "message", "emoji")  # one reaction per user per emoji

    def __str__(self):
        return f"{self.user.username} reacted {self.emoji} to {self.message.id}"

    def to_dict(self):
        return {
            "id": self.id,
            "user": self.user.username,
            "emoji": self.emoji,
            "message_id": self.message.id,
        }
```

---

### 15.2 Add Serializer

`backend/chat/serializers.py`:

```python
from .models import Reaction

class ReactionSerializer(serializers.ModelSerializer):
    class Meta:
        model = Reaction
        fields = ["id", "user", "emoji", "message"]
```

---

### 15.3 Add API View

`backend/chat/views.py`:

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Reaction, Message

class ReactionView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request, message_id):
        emoji = request.data.get("emoji")
        if not emoji:
            return Response({"error": "Emoji required"}, status=status.HTTP_400_BAD_REQUEST)

        message = Message.objects.get(id=message_id)
        reaction, created = Reaction.objects.get_or_create(
            user=request.user, message=message, emoji=emoji
        )

        if not created:
            # toggle off if exists
            reaction.delete()
            return Response({"removed": True})

        # Broadcast reaction via WebSocket
        async_to_sync(channel_layer.group_send)(
            f"message_{message.id}",
            {
                "type": "new_reaction",
                "reaction": reaction.to_dict(),
            },
        )

        return Response(ReactionSerializer(reaction).data)
```

---

### 15.4 Update Consumer to Handle Reactions

`backend/chat/consumers.py`:

```python
async def new_reaction(self, event):
    await self.send(text_data=json.dumps({
        "type": "reaction",
        "reaction": event["reaction"],
    }))
```

---

### 15.5 Add Route

`backend/chat/urls.py`:

```python
urlpatterns += [
    path("messages/<int:message_id>/react/", ReactionView.as_view(), name="react-message"),
]
```

---

## ✅ Frontend Updates

### 15.6 Add Reactions in Chat UI

We’ll:

* Show **emoji buttons** under each message.
* Call API to toggle reactions.
* Listen to WebSocket for live updates.

In `Chat.jsx`:

```jsx
const availableReactions = ["👍", "❤️", "😂", "🔥", "👏"];

const reactToMessage = async (messageId, emoji) => {
  try {
    await axios.post(
      `http://127.0.0.1:8000/api/chat/messages/${messageId}/react/`,
      { emoji },
      { headers: { Authorization: `Bearer ${token}` } }
    );
  } catch (err) {
    console.error("Failed to react", err);
  }
};

useEffect(() => {
  if (!ws.current) return;

  ws.current.onmessage = (event) => {
    const data = JSON.parse(event.data);

    if (data.type === "chat_message") {
      setMessages((prev) => [...prev, data]);
    } else if (data.type === "reaction") {
      setMessages((prev) =>
        prev.map((msg) =>
          msg.id === data.reaction.message_id
            ? {
                ...msg,
                reactions: [...(msg.reactions || []), data.reaction],
              }
            : msg
        )
      );
    } else if (data.type === "typing") {
      if (data.typing) {
        setTypingUsers((prev) => [...new Set([...prev, data.user])]);
      } else {
        setTypingUsers((prev) => prev.filter((u) => u !== data.user));
      }
    }
  };
}, [ws.current]);
```

---

### 15.7 Render Messages with Reactions

```jsx
<div
  className="border p-3 mb-3"
  style={{ height: "300px", overflowY: "scroll" }}
>
  {messages.map((msg, i) => (
    <div key={i} className="mb-2">
      <strong>{msg.sender}:</strong> {msg.content}

      <div className="d-flex align-items-center mt-1">
        {availableReactions.map((emoji) => (
          <span
            key={emoji}
            style={{ cursor: "pointer", marginRight: "5px" }}
            onClick={() => reactToMessage(msg.id, emoji)}
          >
            {emoji}
          </span>
        ))}
      </div>

      {msg.reactions && msg.reactions.length > 0 && (
        <div className="mt-1">
          {msg.reactions.map((r, j) => (
            <span key={j} style={{ marginRight: "8px" }}>
              {r.emoji} ({r.user})
            </span>
          ))}
        </div>
      )}
    </div>
  ))}
</div>
```

---

## ✅ Test Flow

1. User A sends a message.
2. User B clicks 👍 → User A sees the reaction instantly under the message.
3. Clicking again toggles it off (removes reaction).
4. Multiple users can react with different emojis.

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ Emoji reactions under messages
* ✅ Toggle reactions on/off
* ✅ Real-time updates via WebSocket

---

👉 Next upgrade: Do you want me to add **file/image sharing (upload + preview)** so users can send pictures and documents in chat?

Awesome 🚀 — let’s add **file & image sharing** so users can upload and send pictures, documents, or any file in Jeco Chat.

We’ll:

* Extend the **Message model** with an optional `file` field.
* Create an **API for uploading files**.
* Update the **WebSocket consumer** to broadcast messages with files.
* Update the **frontend** to allow file uploads and previews (images show inline, other files show as downloadable links).

---

# 🔄 Step 16 — Add File/Image Sharing

---

## ✅ Backend Updates

### 16.1 Update `Message` Model

`backend/chat/models.py`:

```python
class Message(models.Model):
    sender = models.ForeignKey(User, on_delete=models.CASCADE)
    room = models.ForeignKey(Room, on_delete=models.CASCADE, null=True, blank=True)
    receiver = models.ForeignKey(User, on_delete=models.CASCADE, null=True, blank=True, related_name="received_messages")
    content = models.TextField(blank=True)
    file = models.FileField(upload_to="chat_files/", null=True, blank=True)  # NEW
    timestamp = models.DateTimeField(default=timezone.now)

    def __str__(self):
        return f"{self.sender} -> {self.receiver or self.room}: {self.content or self.file.url}"

    def to_dict(self):
        return {
            "id": self.id,
            "sender": self.sender.username,
            "content": self.content,
            "file": self.file.url if self.file else None,
            "timestamp": self.timestamp.isoformat(),
        }
```

---

### 16.2 Update Serializer

`backend/chat/serializers.py`:

```python
class MessageSerializer(serializers.ModelSerializer):
    sender = serializers.CharField(source="sender.username", read_only=True)

    class Meta:
        model = Message
        fields = ["id", "sender", "content", "file", "timestamp"]
```

---

### 16.3 API for Upload

`backend/chat/views.py`:

```python
from rest_framework.parsers import MultiPartParser, FormParser

class FileMessageView(APIView):
    permission_classes = [IsAuthenticated]
    parser_classes = [MultiPartParser, FormParser]

    def post(self, request, *args, **kwargs):
        room_id = request.data.get("room")
        receiver_id = request.data.get("receiver")
        content = request.data.get("content", "")

        file = request.FILES.get("file")
        if not file:
            return Response({"error": "File is required"}, status=status.HTTP_400_BAD_REQUEST)

        msg = Message.objects.create(
            sender=request.user,
            room=Room.objects.get(id=room_id) if room_id else None,
            receiver=User.objects.get(id=receiver_id) if receiver_id else None,
            content=content,
            file=file,
        )

        # Broadcast via WebSocket
        async_to_sync(channel_layer.group_send)(
            f"room_{room_id}" if room_id else f"private_{receiver_id}",
            {"type": "chat_message", **msg.to_dict()},
        )

        return Response(MessageSerializer(msg).data, status=status.HTTP_201_CREATED)
```

---

### 16.4 Add Route

`backend/chat/urls.py`:

```python
urlpatterns += [
    path("upload/", FileMessageView.as_view(), name="upload-file"),
]
```

---

### 16.5 Media Settings

In `backend/settings.py`:

```python
import os

MEDIA_URL = "/media/"
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
```

In `backend/urls.py`:

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/chat/", include("chat.urls")),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## ✅ Frontend Updates

### 16.6 Add File Input

In `Chat.jsx`:

```jsx
const [file, setFile] = useState(null);

const handleFileChange = (e) => {
  setFile(e.target.files[0]);
};

const sendFile = async () => {
  if (!file) return;

  const formData = new FormData();
  if (roomId) formData.append("room", roomId);
  if (receiverId) formData.append("receiver", receiverId);
  formData.append("file", file);
  formData.append("content", text);

  try {
    await axios.post("http://127.0.0.1:8000/api/chat/upload/", formData, {
      headers: {
        Authorization: `Bearer ${token}`,
        "Content-Type": "multipart/form-data",
      },
    });
    setFile(null);
    setText("");
  } catch (err) {
    console.error("File upload failed", err);
  }
};
```

---

### 16.7 Update UI

Add file input + upload button:

```jsx
<input
  type="file"
  onChange={handleFileChange}
  className="form-control mb-2"
/>
<button className="btn btn-secondary mb-2" onClick={sendFile}>
  Send File
</button>
```

---

### 16.8 Show File Messages

In message rendering:

```jsx
{messages.map((msg, i) => (
  <div key={i} className="mb-2">
    <strong>{msg.sender}:</strong> {msg.content}

    {msg.file && (
      msg.file.match(/\.(jpg|jpeg|png|gif)$/i) ? (
        <div>
          <img
            src={`http://127.0.0.1:8000${msg.file}`}
            alt="chat file"
            style={{ maxWidth: "200px", marginTop: "5px" }}
          />
        </div>
      ) : (
        <div>
          <a
            href={`http://127.0.0.1:8000${msg.file}`}
            target="_blank"
            rel="noreferrer"
          >
            📎 Download File
          </a>
        </div>
      )
    )}
  </div>
))}
```

---

## ✅ Test Flow

1. User selects a file (image or doc).
2. Clicks **Send File** → Uploads via API → Broadcast via WebSocket.
3. Other users instantly see the file:

   * 🖼 Images show inline.
   * 📎 Docs show as clickable download links.

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ Sending images and files.
* ✅ Inline previews for images.
* ✅ Real-time delivery via WebSocket.

---

👉 Next upgrade: Do you want me to add **message notifications (desktop + in-app alerts when a new message arrives)?**

Good catch 👍 — we haven’t explicitly implemented **logout** yet.
Right now, Jeco Chat has **login & token-based authentication** (using DRF SimpleJWT), but users need a way to **log out (invalidate tokens + clear frontend state)**.

Let’s add it:

---

# 🔄 Step 17 — Add Logout Functionality

---

## ✅ Backend Logout

With **JWT (SimpleJWT)**, there are 2 options:

1. **Frontend-only logout (basic)** → Just remove the tokens from local storage.
2. **Blacklist tokens (secure)** → Use `rest_framework_simplejwt.token_blacklist` so refresh tokens can’t be reused.

We’ll do option **2 (secure logout)**.

### 17.1 Enable Blacklist App

In `backend/settings.py`:

```python
INSTALLED_APPS = [
    ...
    "rest_framework_simplejwt.token_blacklist",
]
```

---

### 17.2 Add Logout View

`backend/chat/views.py`:

```python
from rest_framework_simplejwt.tokens import RefreshToken
from rest_framework.permissions import IsAuthenticated

class LogoutView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        try:
            refresh_token = request.data["refresh"]
            token = RefreshToken(refresh_token)
            token.blacklist()
            return Response({"detail": "Logout successful"}, status=status.HTTP_200_OK)
        except Exception:
            return Response({"error": "Invalid token"}, status=status.HTTP_400_BAD_REQUEST)
```

---

### 17.3 Add Route

`backend/chat/urls.py`:

```python
urlpatterns += [
    path("logout/", LogoutView.as_view(), name="logout"),
]
```

---

## ✅ Frontend Logout

In `AuthContext.js`:

```jsx
const logout = async () => {
  try {
    const refresh = localStorage.getItem("refresh");
    if (refresh) {
      await axios.post("http://127.0.0.1:8000/api/chat/logout/", { refresh });
    }
  } catch (err) {
    console.error("Logout failed", err);
  } finally {
    setUser(null);
    localStorage.removeItem("access");
    localStorage.removeItem("refresh");
  }
};
```

---

## ✅ Add Logout Button

In `App.js` (Navbar or Profile dropdown):

```jsx
import { useAuth } from "./context/AuthContext";

function App() {
  const { user, logout } = useAuth();

  return (
    <nav className="navbar navbar-light bg-light">
      <span className="navbar-brand">Jeco Chat</span>
      {user && (
        <button className="btn btn-outline-danger" onClick={logout}>
          Logout
        </button>
      )}
    </nav>
  );
}
```

---

## ✅ Test Flow

1. Login → Access + Refresh tokens stored.
2. Click **Logout** → Refresh token blacklisted, tokens removed from local storage.
3. User redirected to login page.
4. Blacklisted refresh token can’t be reused.

---

## 🎉 Result

Now Jeco Chat supports **secure logout**:

* ✅ Backend: Refresh tokens invalidated.
* ✅ Frontend: Tokens cleared & user session reset.

---

👉 Do you also want me to add **auto-logout when token expires** (force redirect to login if access token is no longer valid)?

Perfect 👍 — let’s implement **Password Reset / Forgot Password** so users can recover their accounts if they forget their password.

We’ll use Django’s built-in password reset system but expose it via **REST API** for the React frontend.

---

# 🔄 Step 18 — Add Password Reset Functionality

---

## ✅ Backend Updates

### 18.1 Install Email Backend (Development Mode)

In development, we’ll use Django’s **console email backend** (emails print in terminal).
In production, you can configure Gmail SMTP or any email provider.

`backend/settings.py`:

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = "noreply@jeco-chat.com"
```

> 🔹 Later for real deployment, replace with SMTP settings.

---

### 18.2 Create Password Reset Views

We’ll create 3 steps:

1. **Request Reset** → user submits email.
2. **Verify Token** → system checks token validity.
3. **Confirm New Password** → user sets new password.

`backend/chat/views.py`:

```python
from django.contrib.auth.tokens import PasswordResetTokenGenerator
from django.utils.encoding import force_bytes, force_str
from django.utils.http import urlsafe_base64_encode, urlsafe_base64_decode
from django.core.mail import send_mail

token_generator = PasswordResetTokenGenerator()

class RequestPasswordReset(APIView):
    permission_classes = [AllowAny]

    def post(self, request):
        email = request.data.get("email")
        try:
            user = User.objects.get(email=email)
        except User.DoesNotExist:
            return Response({"error": "No account with that email"}, status=400)

        uid = urlsafe_base64_encode(force_bytes(user.pk))
        token = token_generator.make_token(user)

        reset_link = f"http://localhost:3000/reset-password/{uid}/{token}/"

        send_mail(
            subject="Jeco Chat Password Reset",
            message=f"Click the link to reset your password: {reset_link}",
            from_email="noreply@jeco-chat.com",
            recipient_list=[email],
        )

        return Response({"detail": "Password reset email sent."}, status=200)


class VerifyPasswordResetToken(APIView):
    permission_classes = [AllowAny]

    def get(self, request, uidb64, token):
        try:
            uid = force_str(urlsafe_base64_decode(uidb64))
            user = User.objects.get(pk=uid)
        except (User.DoesNotExist, ValueError, TypeError, OverflowError):
            return Response({"error": "Invalid link"}, status=400)

        if not token_generator.check_token(user, token):
            return Response({"error": "Invalid or expired token"}, status=400)

        return Response({"detail": "Token valid"}, status=200)


class ResetPassword(APIView):
    permission_classes = [AllowAny]

    def post(self, request, uidb64, token):
        try:
            uid = force_str(urlsafe_base64_decode(uidb64))
            user = User.objects.get(pk=uid)
        except (User.DoesNotExist, ValueError, TypeError, OverflowError):
            return Response({"error": "Invalid link"}, status=400)

        if not token_generator.check_token(user, token):
            return Response({"error": "Invalid or expired token"}, status=400)

        new_password = request.data.get("password")
        if not new_password:
            return Response({"error": "Password required"}, status=400)

        user.set_password(new_password)
        user.save()

        return Response({"detail": "Password reset successful"}, status=200)
```

---

### 18.3 Add Routes

`backend/chat/urls.py`:

```python
urlpatterns += [
    path("password-reset/", RequestPasswordReset.as_view(), name="password-reset"),
    path("password-reset/<uidb64>/<token>/", VerifyPasswordResetToken.as_view(), name="verify-reset"),
    path("password-reset/confirm/<uidb64>/<token>/", ResetPassword.as_view(), name="reset-password"),
]
```

---

## ✅ Frontend Updates

### 18.4 Forgot Password Page

`src/pages/ForgotPassword.js`:

```jsx
import { useState } from "react";
import axios from "axios";

function ForgotPassword() {
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await axios.post("http://127.0.0.1:8000/api/chat/password-reset/", { email });
      setMessage("Password reset link sent! Check your email.");
    } catch (err) {
      setMessage("Error: " + err.response?.data?.error);
    }
  };

  return (
    <div className="container mt-5">
      <h2>Forgot Password</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="email"
          className="form-control mb-2"
          placeholder="Enter your email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
        />
        <button className="btn btn-primary">Send Reset Link</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default ForgotPassword;
```

---

### 18.5 Reset Password Page

`src/pages/ResetPassword.js`:

```jsx
import { useState } from "react";
import { useParams, useNavigate } from "react-router-dom";
import axios from "axios";

function ResetPassword() {
  const { uidb64, token } = useParams();
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await axios.post(`http://127.0.0.1:8000/api/chat/password-reset/confirm/${uidb64}/${token}/`, {
        password,
      });
      setMessage("Password reset successful! Redirecting to login...");
      setTimeout(() => navigate("/login"), 2000);
    } catch (err) {
      setMessage("Error: " + err.response?.data?.error);
    }
  };

  return (
    <div className="container mt-5">
      <h2>Reset Password</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="password"
          className="form-control mb-2"
          placeholder="Enter new password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
        />
        <button className="btn btn-success">Reset Password</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default ResetPassword;
```

---

### 18.6 Update Routes

In `src/App.js`:

```jsx
import ForgotPassword from "./pages/ForgotPassword";
import ResetPassword from "./pages/ResetPassword";

<Routes>
  <Route path="/forgot-password" element={<ForgotPassword />} />
  <Route path="/reset-password/:uidb64/:token/" element={<ResetPassword />} />
</Routes>
```

---

## ✅ Test Flow

1. Go to **Forgot Password** page → enter email.
2. Check Django terminal → you’ll see a reset link (in production it goes to real email).
3. Click link → opens **Reset Password page**.
4. Enter new password → success message → redirected to login.
5. Login with new password ✅.

---

## 🎉 Result

Now Jeco Chat supports **account recovery**:

* ✅ Forgot password via email.
* ✅ Secure token-based reset.
* ✅ Fully integrated frontend + backend.

---

👉 Next logical feature: Should I add **user search + contacts list** (so you don’t need to memorize usernames to start chatting)?

Awesome 🚀 — let’s add a **User Search & Contacts List** so users can easily find people and start chatting without knowing exact usernames.

We’ll:

* Create an **API** to search users.
* Add a **Contact model** so users can save contacts.
* Build a **UI page** to search, add, and manage contacts.

---

# 🔄 Step 19 — Add User Search & Contacts List

---

## ✅ Backend Updates

### 19.1 Extend Models

`backend/chat/models.py`:

```python
class Contact(models.Model):
    owner = models.ForeignKey(User, on_delete=models.CASCADE, related_name="contacts")
    contact_user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="added_by")

    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        unique_together = ("owner", "contact_user")

    def __str__(self):
        return f"{self.owner.username} -> {self.contact_user.username}"

    def to_dict(self):
        return {
            "id": self.id,
            "username": self.contact_user.username,
            "email": self.contact_user.email,
        }
```

---

### 19.2 Serializers

`backend/chat/serializers.py`:

```python
from .models import Contact

class ContactSerializer(serializers.ModelSerializer):
    contact_user = serializers.CharField(source="contact_user.username", read_only=True)

    class Meta:
        model = Contact
        fields = ["id", "contact_user", "created_at"]
```

---

### 19.3 Views

`backend/chat/views.py`:

```python
from rest_framework import generics
from django.db.models import Q

# 🔍 User Search
class UserSearchView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        query = request.query_params.get("q", "")
        users = User.objects.filter(
            Q(username__icontains=query) | Q(email__icontains=query)
        ).exclude(id=request.user.id)[:10]  # limit to 10 results
        return Response(
            [{"id": u.id, "username": u.username, "email": u.email} for u in users]
        )


# 📒 Contacts List
class ContactListView(generics.ListCreateAPIView):
    serializer_class = ContactSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        return Contact.objects.filter(owner=self.request.user)

    def perform_create(self, serializer):
        contact_id = self.request.data.get("contact_user")
        try:
            contact_user = User.objects.get(id=contact_id)
        except User.DoesNotExist:
            raise serializers.ValidationError("User not found")
        serializer.save(owner=self.request.user, contact_user=contact_user)


# ❌ Remove Contact
class ContactDeleteView(generics.DestroyAPIView):
    serializer_class = ContactSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        return Contact.objects.filter(owner=self.request.user)
```

---

### 19.4 Routes

`backend/chat/urls.py`:

```python
urlpatterns += [
    path("users/search/", UserSearchView.as_view(), name="user-search"),
    path("contacts/", ContactListView.as_view(), name="contact-list"),
    path("contacts/<int:pk>/", ContactDeleteView.as_view(), name="delete-contact"),
]
```

---

## ✅ Frontend Updates

### 19.5 Contacts Page

`src/pages/Contacts.js`:

```jsx
import { useState, useEffect } from "react";
import axios from "axios";
import { useAuth } from "../context/AuthContext";

function Contacts() {
  const { token } = useAuth();
  const [search, setSearch] = useState("");
  const [results, setResults] = useState([]);
  const [contacts, setContacts] = useState([]);

  // Load contacts
  useEffect(() => {
    const fetchContacts = async () => {
      try {
        const res = await axios.get("http://127.0.0.1:8000/api/chat/contacts/", {
          headers: { Authorization: `Bearer ${token}` },
        });
        setContacts(res.data);
      } catch (err) {
        console.error("Failed to load contacts", err);
      }
    };
    fetchContacts();
  }, [token]);

  // Search users
  const handleSearch = async (e) => {
    e.preventDefault();
    try {
      const res = await axios.get(
        `http://127.0.0.1:8000/api/chat/users/search/?q=${search}`,
        { headers: { Authorization: `Bearer ${token}` } }
      );
      setResults(res.data);
    } catch (err) {
      console.error("Search failed", err);
    }
  };

  // Add contact
  const addContact = async (id) => {
    try {
      const res = await axios.post(
        "http://127.0.0.1:8000/api/chat/contacts/",
        { contact_user: id },
        { headers: { Authorization: `Bearer ${token}` } }
      );
      setContacts([...contacts, res.data]);
    } catch (err) {
      console.error("Failed to add contact", err);
    }
  };

  // Remove contact
  const removeContact = async (id) => {
    try {
      await axios.delete(`http://127.0.0.1:8000/api/chat/contacts/${id}/`, {
        headers: { Authorization: `Bearer ${token}` },
      });
      setContacts(contacts.filter((c) => c.id !== id));
    } catch (err) {
      console.error("Failed to remove contact", err);
    }
  };

  return (
    <div className="container mt-4">
      <h2>My Contacts</h2>

      {/* Contact List */}
      <ul className="list-group mb-4">
        {contacts.map((c) => (
          <li key={c.id} className="list-group-item d-flex justify-content-between">
            {c.contact_user}
            <button
              className="btn btn-sm btn-danger"
              onClick={() => removeContact(c.id)}
            >
              Remove
            </button>
          </li>
        ))}
      </ul>

      {/* Search */}
      <h3>Find Users</h3>
      <form onSubmit={handleSearch} className="d-flex mb-3">
        <input
          type="text"
          className="form-control me-2"
          placeholder="Search by username or email"
          value={search}
          onChange={(e) => setSearch(e.target.value)}
        />
        <button className="btn btn-primary">Search</button>
      </form>

      {/* Search Results */}
      <ul className="list-group">
        {results.map((u) => (
          <li key={u.id} className="list-group-item d-flex justify-content-between">
            {u.username} ({u.email})
            <button
              className="btn btn-sm btn-success"
              onClick={() => addContact(u.id)}
            >
              Add
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default Contacts;
```

---

### 19.6 Add Route

In `src/App.js`:

```jsx
import Contacts from "./pages/Contacts";

<Routes>
  <Route path="/contacts" element={<Contacts />} />
</Routes>
```

---

## ✅ Test Flow

1. Login as a user → go to **Contacts page**.
2. Search for another user (by username/email).
3. Click **Add** → they appear in **My Contacts**.
4. Remove them → contact disappears.
5. Contacts persist (saved in DB).

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ User search by username/email.
* ✅ Adding/removing contacts.
* ✅ Viewing a personal contact list.

---

👉 Next feature candidate: Should we add **notifications (in-app alerts for new messages)** or **read receipts (✅✅ when someone reads your message)**?

Perfect 🎯 — since you said **yes**, let’s move forward with **in-app notifications**.

This way, users will see an alert or a badge when they receive a new message in **any chat (direct or group)**, even if they’re not currently inside that conversation.

---

# 🔄 Step 20 — Add In-App Notifications

---

## ✅ Backend Updates

We’ll use Django Channels (WebSockets) since we already have them running for messages.

### 20.1 Extend Models

`backend/chat/models.py`

```python
class Notification(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="notifications")
    message = models.ForeignKey(Message, on_delete=models.CASCADE, null=True, blank=True)
    text = models.CharField(max_length=255)
    is_read = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"Notification for {self.user.username}: {self.text}"
```

---

### 20.2 Serializers

`backend/chat/serializers.py`

```python
from .models import Notification

class NotificationSerializer(serializers.ModelSerializer):
    class Meta:
        model = Notification
        fields = ["id", "text", "is_read", "created_at"]
```

---

### 20.3 Views

`backend/chat/views.py`

```python
# List & mark notifications as read
class NotificationListView(generics.ListAPIView):
    serializer_class = NotificationSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        return Notification.objects.filter(user=self.request.user).order_by("-created_at")


class MarkNotificationReadView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request, pk):
        try:
            notif = Notification.objects.get(id=pk, user=request.user)
            notif.is_read = True
            notif.save()
            return Response({"status": "read"})
        except Notification.DoesNotExist:
            return Response({"error": "Not found"}, status=404)
```

---

### 20.4 Routes

`backend/chat/urls.py`

```python
urlpatterns += [
    path("notifications/", NotificationListView.as_view(), name="notification-list"),
    path("notifications/<int:pk>/read/", MarkNotificationReadView.as_view(), name="notification-read"),
]
```

---

### 20.5 Hook into Message Creation

Whenever a **message is sent**, create a notification for all recipients.

Update `MessageConsumer` in `backend/chat/consumers.py`:

```python
from .models import Notification

async def save_message(self, content):
    message = Message.objects.create(
        room=self.room,
        user=self.user,
        text=content["text"]
    )

    # Create notification for all other members
    members = self.room.members.exclude(id=self.user.id)
    for m in members:
        Notification.objects.create(
            user=m,
            message=message,
            text=f"New message from {self.user.username} in {self.room.name}"
        )

    return message
```

---

## ✅ Frontend Updates

### 20.6 Context for Notifications

`src/context/NotificationContext.js`

```jsx
import { createContext, useState, useEffect, useContext } from "react";
import axios from "axios";
import { useAuth } from "./AuthContext";

const NotificationContext = createContext();

export function NotificationProvider({ children }) {
  const { token } = useAuth();
  const [notifications, setNotifications] = useState([]);

  // Load notifications
  useEffect(() => {
    if (!token) return;
    const fetchNotifications = async () => {
      try {
        const res = await axios.get("http://127.0.0.1:8000/api/chat/notifications/", {
          headers: { Authorization: `Bearer ${token}` },
        });
        setNotifications(res.data);
      } catch (err) {
        console.error("Failed to load notifications", err);
      }
    };
    fetchNotifications();

    const interval = setInterval(fetchNotifications, 5000); // poll every 5s
    return () => clearInterval(interval);
  }, [token]);

  const markRead = async (id) => {
    try {
      await axios.post(
        `http://127.0.0.1:8000/api/chat/notifications/${id}/read/`,
        {},
        { headers: { Authorization: `Bearer ${token}` } }
      );
      setNotifications(notifications.map(n => 
        n.id === id ? { ...n, is_read: true } : n
      ));
    } catch (err) {
      console.error("Failed to mark read", err);
    }
  };

  return (
    <NotificationContext.Provider value={{ notifications, markRead }}>
      {children}
    </NotificationContext.Provider>
  );
}

export function useNotifications() {
  return useContext(NotificationContext);
}
```

---

### 20.7 Show Notifications in Navbar

Update `src/components/Navbar.js`:

```jsx
import { useNotifications } from "../context/NotificationContext";

function Navbar() {
  const { notifications } = useNotifications();
  const unreadCount = notifications.filter(n => !n.is_read).length;

  return (
    <nav className="navbar navbar-dark bg-dark px-3">
      <a className="navbar-brand" href="/">Jeco Chat</a>
      <div>
        <a href="/contacts" className="btn btn-outline-light me-2">Contacts</a>
        <a href="/notifications" className="btn btn-outline-warning">
          Notifications {unreadCount > 0 && <span>({unreadCount})</span>}
        </a>
      </div>
    </nav>
  );
}
```

---

### 20.8 Notifications Page

`src/pages/Notifications.js`:

```jsx
import { useNotifications } from "../context/NotificationContext";

function Notifications() {
  const { notifications, markRead } = useNotifications();

  return (
    <div className="container mt-4">
      <h2>Notifications</h2>
      <ul className="list-group">
        {notifications.map((n) => (
          <li
            key={n.id}
            className={`list-group-item d-flex justify-content-between ${
              n.is_read ? "" : "list-group-item-warning"
            }`}
          >
            {n.text}
            {!n.is_read && (
              <button
                className="btn btn-sm btn-primary"
                onClick={() => markRead(n.id)}
              >
                Mark Read
              </button>
            )}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default Notifications;
```

---

### 20.9 Add Route

`src/App.js`

```jsx
import Notifications from "./pages/Notifications";

<Routes>
  <Route path="/notifications" element={<Notifications />} />
</Routes>
```

---

## ✅ Test Flow

1. Login with **User A** and **User B**.
2. User A sends a message → User B gets a **notification** in navbar.
3. User B clicks **Notifications** → sees unread message.
4. Marks it **read** → badge count decreases.

---

## 🎉 Result

Now Jeco Chat has:

* ✅ Automatic notifications for new messages.
* ✅ Navbar badge for unread notifications.
* ✅ Notifications page to view & mark as read.

---

👉 Do you want me to add **read receipts (✅✅ when someone opens your message)** next, or would you prefer we polish notifications with **real-time WebSocket push (instead of 5s polling)**?

Alright 🚀 — since you said **yes**, let’s move to **Read Receipts (✅✅ when a message is read)**.
This will let users know if their message has been **delivered** and **read** in real-time.

---

# 🔄 Step 21 — Add Read Receipts

---

## ✅ Backend Updates

### 21.1 Extend Models

`backend/chat/models.py`

```python
class MessageReadReceipt(models.Model):
    message = models.ForeignKey(Message, on_delete=models.CASCADE, related_name="read_receipts")
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    read_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        unique_together = ("message", "user")

    def __str__(self):
        return f"{self.user.username} read {self.message.id} at {self.read_at}"
```

---

### 21.2 Serializers

`backend/chat/serializers.py`

```python
from .models import MessageReadReceipt

class MessageReadReceiptSerializer(serializers.ModelSerializer):
    class Meta:
        model = MessageReadReceipt
        fields = ["user", "read_at"]
```

---

### 21.3 Views

We need an endpoint to **mark a message as read**.

`backend/chat/views.py`

```python
class MarkMessageReadView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request, pk):
        try:
            message = Message.objects.get(id=pk)
            if request.user not in message.room.members.all():
                return Response({"error": "Not allowed"}, status=403)

            receipt, created = MessageReadReceipt.objects.get_or_create(
                message=message, user=request.user
            )
            return Response({"status": "read", "message": message.id})
        except Message.DoesNotExist:
            return Response({"error": "Message not found"}, status=404)
```

---

### 21.4 Routes

`backend/chat/urls.py`

```python
urlpatterns += [
    path("messages/<int:pk>/read/", MarkMessageReadView.as_view(), name="message-read"),
]
```

---

### 21.5 WebSocket Broadcast (Optional but Cool)

In `backend/chat/consumers.py`, whenever someone marks a message as read, we notify other users in that chat room.

```python
async def message_read(self, event):
    await self.send(text_data=json.dumps({
        "type": "message_read",
        "message_id": event["message_id"],
        "user": event["user"]
    }))
```

And when saving a read receipt in `MarkMessageReadView`, trigger it via channel layer.

---

## ✅ Frontend Updates

### 21.6 Update Chat Component

When a user **opens a room**, mark messages as read.

`src/pages/ChatRoom.js`

```jsx
import { useEffect } from "react";
import axios from "axios";
import { useAuth } from "../context/AuthContext";

function ChatRoom({ roomId, messages, setMessages }) {
  const { token } = useAuth();

  // Mark all messages as read when entering
  useEffect(() => {
    const markAllRead = async () => {
      try {
        for (let msg of messages) {
          if (msg.user !== "me") {
            await axios.post(
              `http://127.0.0.1:8000/api/chat/messages/${msg.id}/read/`,
              {},
              { headers: { Authorization: `Bearer ${token}` } }
            );
          }
        }
      } catch (err) {
        console.error("Failed to mark read", err);
      }
    };
    if (messages.length > 0) {
      markAllRead();
    }
  }, [messages, token]);

  return (
    <div>
      {messages.map((msg) => (
        <div key={msg.id} className="mb-2">
          <strong>{msg.user}</strong>: {msg.text}
          {/* Read receipt indicator */}
          {msg.read_by?.length > 0 && (
            <span className="text-muted ms-2">
              ✅ Read by {msg.read_by.join(", ")}
            </span>
          )}
        </div>
      ))}
    </div>
  );
}

export default ChatRoom;
```

---

### 21.7 Modify Message Fetch API

When fetching messages, include **who has read them**.

In `backend/chat/serializers.py`:

```python
class MessageSerializer(serializers.ModelSerializer):
    read_by = serializers.SerializerMethodField()

    class Meta:
        model = Message
        fields = ["id", "user", "text", "created_at", "read_by"]

    def get_read_by(self, obj):
        return [r.user.username for r in obj.read_receipts.all()]
```

Now the frontend can show ✅ next to messages read.

---

## ✅ Test Flow

1. Login as **User A** and **User B**.
2. A sends a message in a chat room.
3. B enters the room → message gets marked **read**.
4. A sees ✅ “Read by B” under the message.
5. Works for group chats too (shows multiple usernames).

---

## 🎉 Result

Now Jeco Chat supports:

* ✅ Read receipts (who read a message).
* ✅ Live updates when others read your message.
* ✅ Works for 1-to-1 and group chats.

---

👉 Do you want me to now **improve notifications to real-time WebSocket push (instead of polling every 5s)**, so they update instantly?

Perfect 👍 — since you said **yes**, I’ll move ahead with the **Password Reset / Forgot Password** flow.

This is a **critical feature** for any real app because users will inevitably forget their password. We’ll implement it step by step:

---

# 🔄 Step 22 — Password Reset Flow

---

## ✅ Backend Updates

We’ll use Django’s built-in **password reset tools** + DRF views.

### 22.1 Update Settings

In `backend/backend/settings.py`, configure **email sending**. For development, we’ll just log emails to the console:

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = "noreply@jeco-chat.com"
```

👉 In production, you’d configure SMTP (e.g., Gmail, SendGrid, Mailgun).

---

### 22.2 Create Password Reset Views

`backend/chat/views.py`

```python
from django.contrib.auth.tokens import PasswordResetTokenGenerator
from django.utils.http import urlsafe_base64_encode, urlsafe_base64_decode
from django.utils.encoding import force_bytes, force_str
from django.core.mail import send_mail

token_generator = PasswordResetTokenGenerator()

# 1️⃣ Request Password Reset
class RequestPasswordResetView(APIView):
    permission_classes = [AllowAny]

    def post(self, request):
        email = request.data.get("email")
        try:
            user = User.objects.get(email=email)
            uid = urlsafe_base64_encode(force_bytes(user.pk))
            token = token_generator.make_token(user)
            reset_link = f"http://localhost:3000/reset-password/{uid}/{token}/"

            # Send email (console in dev)
            send_mail(
                "Jeco Chat Password Reset",
                f"Click here to reset your password: {reset_link}",
                "noreply@jeco-chat.com",
                [email],
            )

            return Response({"message": "Password reset link sent to email"})
        except User.DoesNotExist:
            return Response({"error": "User not found"}, status=404)


# 2️⃣ Confirm Reset
class ResetPasswordConfirmView(APIView):
    permission_classes = [AllowAny]

    def post(self, request, uidb64, token):
        try:
            uid = force_str(urlsafe_base64_decode(uidb64))
            user = User.objects.get(pk=uid)
            if not token_generator.check_token(user, token):
                return Response({"error": "Invalid or expired token"}, status=400)

            new_password = request.data.get("password")
            user.set_password(new_password)
            user.save()
            return Response({"message": "Password reset successful"})
        except Exception:
            return Response({"error": "Invalid request"}, status=400)
```

---

### 22.3 Add Routes

`backend/chat/urls.py`

```python
urlpatterns += [
    path("password-reset/", RequestPasswordResetView.as_view(), name="password-reset"),
    path(
        "password-reset-confirm/<uidb64>/<token>/",
        ResetPasswordConfirmView.as_view(),
        name="password-reset-confirm",
    ),
]
```

---

## ✅ Frontend Updates

### 22.4 Password Reset Request Page

`src/pages/ForgotPassword.js`

```jsx
import { useState } from "react";
import axios from "axios";

function ForgotPassword() {
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const res = await axios.post("http://127.0.0.1:8000/api/chat/password-reset/", {
        email,
      });
      setMessage(res.data.message);
    } catch (err) {
      setMessage("Failed to send reset link");
    }
  };

  return (
    <div className="container mt-4">
      <h2>Forgot Password</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="email"
          className="form-control mb-2"
          placeholder="Enter your email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <button className="btn btn-primary">Send Reset Link</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default ForgotPassword;
```

---

### 22.5 Password Reset Confirm Page

`src/pages/ResetPassword.js`

```jsx
import { useState } from "react";
import { useParams } from "react-router-dom";
import axios from "axios";

function ResetPassword() {
  const { uidb64, token } = useParams();
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const res = await axios.post(
        `http://127.0.0.1:8000/api/chat/password-reset-confirm/${uidb64}/${token}/`,
        { password }
      );
      setMessage(res.data.message);
    } catch (err) {
      setMessage("Failed to reset password");
    }
  };

  return (
    <div className="container mt-4">
      <h2>Reset Password</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="password"
          className="form-control mb-2"
          placeholder="Enter new password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
        <button className="btn btn-success">Reset Password</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default ResetPassword;
```

---

### 22.6 Add Routes

`src/App.js`

```jsx
import ForgotPassword from "./pages/ForgotPassword";
import ResetPassword from "./pages/ResetPassword";

<Routes>
  <Route path="/forgot-password" element={<ForgotPassword />} />
  <Route path="/reset-password/:uidb64/:token/" element={<ResetPassword />} />
</Routes>
```

---

## ✅ Test Flow

1. Go to `/forgot-password` → enter your email.
2. Check console → you’ll see a **reset link**.
3. Open the link → reset form appears.
4. Enter new password → login with it.

---

## 🎉 Result

Now Jeco Chat supports **full password reset**:

* ✅ Request reset link by email.
* ✅ Secure token validation.
* ✅ Set new password.
* ✅ Works with frontend pages.

---

👉 With **Password Reset** done, all **critical features** are now implemented.

Would you like me to now add **Profile Pictures / Avatars** (so users have more identity in chats)?

Perfect 👍 — let’s move ahead with **Profile Pictures / Avatars**.

---

# 🖼️ Step 23 — User Profile Pictures

---

## ✅ Backend Updates

We’ll extend the `User` model with an avatar field.

### 23.1 Update User Model

`backend/chat/models.py`

```python
class User(AbstractUser):
    email = models.EmailField(unique=True)
    avatar = models.ImageField(upload_to="avatars/", null=True, blank=True)  # NEW
```

👉 This will store profile pictures under `media/avatars/`.

---

### 23.2 Update Settings

`backend/backend/settings.py`

```python
import os

MEDIA_URL = "/media/"
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
```

---

### 23.3 Update Serializer

`backend/chat/serializers.py`

```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ["id", "username", "email", "avatar"]  # add avatar
```

---

### 23.4 Update Views

We’ll allow profile updates, including avatars.

`backend/chat/views.py`

```python
from rest_framework.parsers import MultiPartParser, FormParser

class ProfileUpdateView(generics.UpdateAPIView):
    serializer_class = UserSerializer
    queryset = User.objects.all()
    parser_classes = [MultiPartParser, FormParser]

    def get_object(self):
        return self.request.user
```

---

### 23.5 Add Routes

`backend/chat/urls.py`

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += [
    path("profile/update/", ProfileUpdateView.as_view(), name="profile-update"),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## ✅ Frontend Updates

We’ll create a **Profile Page** where users can upload avatars.

### 23.6 Profile Page

`src/pages/Profile.js`

```jsx
import { useState } from "react";
import axios from "axios";

function Profile() {
  const [avatar, setAvatar] = useState(null);
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();
    const formData = new FormData();
    if (avatar) formData.append("avatar", avatar);

    try {
      await axios.put("http://127.0.0.1:8000/api/chat/profile/update/", formData, {
        headers: {
          Authorization: `Bearer ${localStorage.getItem("token")}`,
          "Content-Type": "multipart/form-data",
        },
      });
      setMessage("Profile updated successfully");
    } catch (err) {
      setMessage("Failed to update profile");
    }
  };

  return (
    <div className="container mt-4">
      <h2>My Profile</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="file"
          className="form-control mb-2"
          accept="image/*"
          onChange={(e) => setAvatar(e.target.files[0])}
        />
        <button className="btn btn-primary">Update Profile</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default Profile;
```

---

### 23.7 Show Avatars in Chat

In `src/pages/Chat.js`, update message display:

```jsx
{messages.map((msg) => (
  <div key={msg.id} className="mb-2">
    <strong>
      {msg.sender.avatar ? (
        <img
          src={`http://127.0.0.1:8000${msg.sender.avatar}`}
          alt="avatar"
          width="30"
          height="30"
          style={{ borderRadius: "50%", marginRight: "8px" }}
        />
      ) : (
        "👤 "
      )}
      {msg.sender.username}:
    </strong>{" "}
    {msg.content}
  </div>
))}
```

---

### 23.8 Add Route

`src/App.js`

```jsx
import Profile from "./pages/Profile";

<Routes>
  <Route path="/profile" element={<Profile />} />
</Routes>
```

---

## ✅ Test Flow

1. Login → go to `/profile`.
2. Upload a picture → save profile.
3. Go to chats → messages now display avatar next to username.
4. If no avatar, default icon 👤 shows.

---

## 🎉 Result

Now Jeco Chat supports **user avatars**:

* ✅ Upload profile pictures.
* ✅ Store in media folder.
* ✅ Show in chat UI.
* ✅ Default fallback icon if missing.

---

👉 With this done, **users now have an identity in chats** 🎨.

Do you want me to move next into **Better Notifications (real-time WebSockets instead of 5s polling)**, or would you prefer I do **Message Search** first?

Perfect — let’s implement **Message Search** 🔍.

---

# 📝 Step 24 — Message Search (Backend + Frontend)

---

## ✅ Backend Implementation

We’ll expose a **search endpoint** that allows users to query messages by keyword.

---

### 24.1 Update Views

`backend/chat/views.py`

```python
from rest_framework import generics, permissions
from django.db.models import Q
from .models import Message
from .serializers import MessageSerializer

class MessageSearchView(generics.ListAPIView):
    serializer_class = MessageSerializer
    permission_classes = [permissions.IsAuthenticated]

    def get_queryset(self):
        query = self.request.query_params.get("q", "")
        return Message.objects.filter(
            Q(content__icontains=query),
            Q(sender=self.request.user) | Q(room__members=self.request.user)
        ).order_by("-timestamp")
```

👉 This ensures:

* Users can **only search messages they are part of** (security).
* `q` query param is matched against `content`.

---

### 24.2 Add URL Route

`backend/chat/urls.py`

```python
from .views import MessageSearchView

urlpatterns += [
    path("messages/search/", MessageSearchView.as_view(), name="message-search"),
]
```

---

### 24.3 Example API Usage

```
GET /api/chat/messages/search/?q=hello
Authorization: Bearer <token>
```

Response:

```json
[
  {
    "id": 15,
    "sender": { "id": 2, "username": "alice" },
    "content": "hello team!",
    "timestamp": "2025-09-03T07:40:00Z",
    "room": 1
  }
]
```

---

## ✅ Frontend Implementation

We’ll add a **search bar** in the chat page.

---

### 24.4 Update Chat Page

`src/pages/Chat.js`

```jsx
import { useState } from "react";

function Chat() {
  const [messages, setMessages] = useState([]);
  const [searchQuery, setSearchQuery] = useState("");
  const [searchResults, setSearchResults] = useState([]);

  const handleSearch = async (e) => {
    e.preventDefault();
    try {
      const res = await fetch(
        `http://127.0.0.1:8000/api/chat/messages/search/?q=${searchQuery}`,
        {
          headers: {
            Authorization: `Bearer ${localStorage.getItem("token")}`,
          },
        }
      );
      const data = await res.json();
      setSearchResults(data);
    } catch (err) {
      console.error("Search failed", err);
    }
  };

  return (
    <div className="container mt-4">
      <h2>Chat Room</h2>

      {/* Search Bar */}
      <form onSubmit={handleSearch} className="mb-3 d-flex">
        <input
          type="text"
          placeholder="Search messages..."
          className="form-control me-2"
          value={searchQuery}
          onChange={(e) => setSearchQuery(e.target.value)}
        />
        <button className="btn btn-secondary">Search</button>
      </form>

      {/* Search Results */}
      {searchResults.length > 0 && (
        <div className="mb-3">
          <h5>Search Results</h5>
          <ul className="list-group">
            {searchResults.map((msg) => (
              <li key={msg.id} className="list-group-item">
                <strong>{msg.sender.username}:</strong> {msg.content}{" "}
                <small className="text-muted">
                  ({new Date(msg.timestamp).toLocaleString()})
                </small>
              </li>
            ))}
          </ul>
        </div>
      )}

      {/* Regular Chat Messages */}
      <div>
        {messages.map((msg) => (
          <div key={msg.id} className="mb-2">
            <strong>{msg.sender.username}:</strong> {msg.content}
          </div>
        ))}
      </div>
    </div>
  );
}

export default Chat;
```

---

## ✅ Test Flow

1. Open a chat.
2. Enter a keyword (e.g., "hello") in the search box.
3. Search results appear **separately above the chat messages**.
4. If no results → nothing shows.

---

## 🎉 Result

* ✅ Search messages across all conversations you’re part of.
* ✅ Secure (only searches your messages + your rooms).
* ✅ Results show sender, message, and timestamp.

---

👉 Now Jeco Chat supports **Message Search** 🔍.

Would you like me to move on to **Better Notifications (real-time WebSockets instead of polling)** next?


Awesome 🚀 — let’s upgrade **Notifications** from basic polling to **real-time WebSockets**.

---

# 🔔 Step 25 — Real-Time Notifications

Right now, Jeco Chat checks for new messages every **5 seconds (polling)**. That’s wasteful and slow. We’ll switch to **Django Channels + WebSockets** for instant notifications.

---

## ✅ Backend (Django)

We already have **Channels** set up for chat messages. Now we’ll add a **notifications consumer**.

---

### 25.1 Create Notifications Consumer

`backend/chat/consumers.py`

```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class NotificationConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        if self.scope["user"].is_anonymous:
            await self.close()
        else:
            self.group_name = f"user_{self.scope['user'].id}"
            await self.channel_layer.group_add(self.group_name, self.channel_name)
            await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.group_name, self.channel_name)

    async def notify(self, event):
        await self.send(text_data=json.dumps(event["content"]))
```

👉 Each user has their own private group (`user_<id>`).

---

### 25.2 Update Routing

`backend/chat/routing.py`

```python
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r"ws/chat/(?P<room_name>\w+)/$", consumers.ChatConsumer.as_asgi()),
    re_path(r"ws/notifications/$", consumers.NotificationConsumer.as_asgi()),  # NEW
]
```

---

### 25.3 Trigger Notifications

We’ll send a notification when a **new message** is created.

`backend/chat/views.py` → inside `send_message` view:

```python
from asgiref.sync import async_to_sync
from channels.layers import get_channel_layer

channel_layer = get_channel_layer()

# after saving message:
for member in room.members.exclude(id=request.user.id):
    async_to_sync(channel_layer.group_send)(
        f"user_{member.id}",
        {
            "type": "notify",
            "content": {
                "type": "new_message",
                "room_id": room.id,
                "room_name": room.name,
                "sender": request.user.username,
                "message": message.content,
            },
        },
    )
```

👉 Every other room member gets a **new message notification**.

---

## ✅ Frontend (React)

We’ll open a **WebSocket connection** just for notifications.

---

### 25.4 Create Notification Hook

`src/hooks/useNotifications.js`

```jsx
import { useEffect } from "react";

function useNotifications(onNotify) {
  useEffect(() => {
    const token = localStorage.getItem("token");
    if (!token) return;

    const socket = new WebSocket("ws://127.0.0.1:8000/ws/notifications/");

    socket.onmessage = (event) => {
      const data = JSON.parse(event.data);
      if (onNotify) onNotify(data);
    };

    socket.onclose = () => {
      console.log("Notifications socket closed");
    };

    return () => socket.close();
  }, [onNotify]);
}

export default useNotifications;
```

---

### 25.5 Show Notifications

We’ll add a **toast-style alert**.

`src/components/Notifications.js`

```jsx
import { useState } from "react";
import useNotifications from "../hooks/useNotifications";

function Notifications() {
  const [alerts, setAlerts] = useState([]);

  useNotifications((data) => {
    setAlerts((prev) => [...prev, data]);
    setTimeout(() => setAlerts((prev) => prev.slice(1)), 5000); // auto-hide
  });

  return (
    <div
      style={{
        position: "fixed",
        top: "20px",
        right: "20px",
        zIndex: 9999,
      }}
    >
      {alerts.map((alert, index) => (
        <div key={index} className="alert alert-info shadow">
          <strong>{alert.sender}</strong>: {alert.message}
          <br />
          <small>in {alert.room_name}</small>
        </div>
      ))}
    </div>
  );
}

export default Notifications;
```

---

### 25.6 Integrate Into App

`src/App.js`

```jsx
import Notifications from "./components/Notifications";

function App() {
  return (
    <>
      <Notifications />
      {/* rest of routes */}
    </>
  );
}
```

---

## ✅ Test Flow

1. Open app in **two browsers** (Alice + Bob).
2. Alice sends a message in a group.
3. Bob instantly sees a **popup notification** (no refresh, no polling).
4. Notifications auto-hide after 5 seconds.

---

## 🎉 Result

* ✅ Real-time notifications via WebSockets.
* ✅ No more wasteful polling.
* ✅ Toast-style popup messages.
* ✅ Scales per user (private notification channel).

---

👉 With **Notifications** done, Jeco Chat now feels **much closer to WhatsApp / Messenger**.

Would you like me to go next into **Deployment Setup (production-ready: Gunicorn, Nginx, React build, Docker option)**, or finish smaller features like **Dark Mode + Message Pinning** first?


Great 🚀 — since you confirmed, I’ll take you into the **Deployment Setup** next, because it’s critical for making Jeco Chat production-ready.

---

# 🌍 Step 26 — Deployment Setup (Production Ready)

We’ll move Jeco Chat from “developer mode” to something you can actually **host on a VPS or cloud**.

---

## ✅ 26.1 Prepare Django Backend for Production

### Install Gunicorn (WSGI server)

```bash
pip install gunicorn
```

### Run Gunicorn (test locally)

```bash
gunicorn backend.wsgi:application --bind 0.0.0.0:8000
```

👉 This replaces `python manage.py runserver` in production.

---

### Configure Static & Media Files

`backend/settings.py`

```python
STATIC_URL = "/static/"
STATIC_ROOT = BASE_DIR / "staticfiles"

MEDIA_URL = "/media/"
MEDIA_ROOT = BASE_DIR / "media"
```

Collect static files:

```bash
python manage.py collectstatic
```

---

## ✅ 26.2 Nginx as Reverse Proxy

Nginx will:

* Serve **React build (frontend)**
* Proxy **API requests** to Django/Gunicorn
* Handle **WebSockets** for chat/notifications

Example config: `/etc/nginx/sites-available/jeco_chat`

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        root /var/www/jeco_chat/frontend/build;
        index index.html;
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /ws/ {
        proxy_pass http://127.0.0.1:8001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }

    location /static/ {
        alias /var/www/jeco_chat/backend/staticfiles/;
    }

    location /media/ {
        alias /var/www/jeco_chat/backend/media/;
    }
}
```

Enable site:

```bash
sudo ln -s /etc/nginx/sites-available/jeco_chat /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

---

## ✅ 26.3 React Frontend Build

In the frontend folder:

```bash
npm run build
```

This creates a **production-ready build** in `build/` that Nginx serves.

---

## ✅ 26.4 WebSockets (Daphne or Uvicorn)

Gunicorn can’t handle WebSockets.
We’ll run **Daphne** (or Uvicorn) for Channels.

Install:

```bash
pip install daphne
```

Run:

```bash
daphne -b 0.0.0.0 -p 8001 backend.asgi:application
```

👉 Now:

* Gunicorn serves API (`:8000`)
* Daphne serves WebSockets (`:8001`)
* Nginx proxies both correctly

---

## ✅ 26.5 Systemd Services (Autostart)

Create `/etc/systemd/system/jeco_chat_backend.service`:

```ini
[Unit]
Description=Jeco Chat Django Backend
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/jeco_chat/backend
ExecStart=/var/www/jeco_chat/venv/bin/gunicorn backend.wsgi:application --bind 127.0.0.1:8000
Restart=always

[Install]
WantedBy=multi-user.target
```

Create `/etc/systemd/system/jeco_chat_ws.service`:

```ini
[Unit]
Description=Jeco Chat WebSockets
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/jeco_chat/backend
ExecStart=/var/www/jeco_chat/venv/bin/daphne -b 127.0.0.1 -p 8001 backend.asgi:application
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable:

```bash
sudo systemctl enable jeco_chat_backend
sudo systemctl start jeco_chat_backend
sudo systemctl enable jeco_chat_ws
sudo systemctl start jeco_chat_ws
```

---

## ✅ 26.6 (Optional) Docker Setup

If you want **Dockerized deployment**, we’d add:

`Dockerfile.backend`

```dockerfile
FROM python:3.11
WORKDIR /app
COPY backend/ .
RUN pip install -r requirements.txt
CMD ["gunicorn", "backend.wsgi:application", "--bind", "0.0.0.0:8000"]
```

`Dockerfile.frontend`

```dockerfile
FROM node:18
WORKDIR /app
COPY frontend/ .
RUN npm install && npm run build
```

Then use **docker-compose** to orchestrate Django, Postgres, and React build.

---

## 🎉 Result

* ✅ Backend served by **Gunicorn** (fast, reliable).
* ✅ WebSockets handled by **Daphne**.
* ✅ Nginx serving React build + static files.
* ✅ Systemd keeps services alive.
* ✅ Optional Docker for containerization.

---

👉 With deployment ready, Jeco Chat is **production-ready**.

Would you like me to move next into **smaller UX features (Dark Mode + Message Pinning)**, or finish **Password Reset flow** first?

Perfect 👍 — since you confirmed, I’ll now take you into the **Password Reset (Forgot Password) Flow**, which is the last *critical* missing piece for real-world users.

---

# 🔑 Step 27 — Password Reset (Forgot Password)

We’ll implement a secure **password reset** flow using Django’s built-in tools + email.

---

## ✅ 27.1 Backend Implementation

### Install Email Backend (for dev)

Add this to `backend/settings.py`:

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
```

👉 In development, reset emails will just print to the terminal.
👉 In production, you’d configure real SMTP (e.g., Gmail, SendGrid).

---

### 27.2 Create Password Reset Views

`backend/chat/views.py`

```python
from django.contrib.auth.tokens import PasswordResetTokenGenerator
from django.core.mail import send_mail
from django.utils.encoding import force_bytes, force_str
from django.utils.http import urlsafe_base64_encode, urlsafe_base64_decode
from django.contrib.auth import get_user_model
from rest_framework.response import Response
from rest_framework import status

User = get_user_model()
token_generator = PasswordResetTokenGenerator()


class PasswordResetRequestView(APIView):
    def post(self, request):
        email = request.data.get("email")
        try:
            user = User.objects.get(email=email)
            uid = urlsafe_base64_encode(force_bytes(user.pk))
            token = token_generator.make_token(user)

            reset_link = f"http://localhost:3000/reset-password/{uid}/{token}/"

            send_mail(
                "Password Reset Request",
                f"Click here to reset your password: {reset_link}",
                "no-reply@jeco-chat.com",
                [user.email],
                fail_silently=True,
            )

        except User.DoesNotExist:
            pass  # Don’t reveal email existence

        return Response({"message": "If that email exists, a reset link was sent."})


class PasswordResetConfirmView(APIView):
    def post(self, request, uidb64, token):
        try:
            uid = force_str(urlsafe_base64_decode(uidb64))
            user = User.objects.get(pk=uid)

            if not token_generator.check_token(user, token):
                return Response({"error": "Invalid or expired token"}, status=400)

            new_password = request.data.get("password")
            user.set_password(new_password)
            user.save()
            return Response({"message": "Password reset successful"})

        except Exception:
            return Response({"error": "Invalid request"}, status=400)
```

---

### 27.3 Add URLs

`backend/chat/urls.py`

```python
from .views import PasswordResetRequestView, PasswordResetConfirmView

urlpatterns += [
    path("password-reset/", PasswordResetRequestView.as_view(), name="password-reset"),
    path("password-reset-confirm/<uidb64>/<token>/", PasswordResetConfirmView.as_view(), name="password-reset-confirm"),
]
```

---

## ✅ 27.4 Frontend Implementation

We’ll create **two pages**:

1. **Request Reset (enter email)**
2. **Reset Password (new password form)**

---

### 27.4.1 Request Reset Page

`src/pages/PasswordResetRequest.js`

```jsx
import { useState } from "react";

function PasswordResetRequest() {
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();
    const res = await fetch("http://127.0.0.1:8000/api/chat/password-reset/", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ email }),
    });
    const data = await res.json();
    setMessage(data.message);
  };

  return (
    <div className="container mt-4">
      <h2>Forgot Password</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="email"
          className="form-control mb-2"
          placeholder="Enter your email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <button className="btn btn-primary">Send Reset Link</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default PasswordResetRequest;
```

---

### 27.4.2 Reset Password Page

`src/pages/PasswordResetConfirm.js`

```jsx
import { useState } from "react";
import { useParams } from "react-router-dom";

function PasswordResetConfirm() {
  const { uidb64, token } = useParams();
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();
    const res = await fetch(
      `http://127.0.0.1:8000/api/chat/password-reset-confirm/${uidb64}/${token}/`,
      {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ password }),
      }
    );
    const data = await res.json();
    setMessage(data.message || data.error);
  };

  return (
    <div className="container mt-4">
      <h2>Reset Password</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="password"
          className="form-control mb-2"
          placeholder="Enter new password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
        <button className="btn btn-success">Reset Password</button>
      </form>
      {message && <p className="mt-3">{message}</p>}
    </div>
  );
}

export default PasswordResetConfirm;
```

---

### 27.4.3 Add Routes

`src/App.js`

```jsx
import PasswordResetRequest from "./pages/PasswordResetRequest";
import PasswordResetConfirm from "./pages/PasswordResetConfirm";

<Routes>
  <Route path="/forgot-password" element={<PasswordResetRequest />} />
  <Route path="/reset-password/:uidb64/:token" element={<PasswordResetConfirm />} />
</Routes>
```

---

## ✅ 27.5 Test Flow

1. Go to `/forgot-password`.
2. Enter email → check Django console for reset link.
3. Open reset link → `/reset-password/:uid/:token`.
4. Enter new password → login works with new password.

---

## 🎉 Result

* ✅ Users can request a reset link.
* ✅ Secure token-based reset.
* ✅ Works via email (console for dev).
* ✅ Protects user privacy (always same message even if email not found).

---

👉 With this, **Jeco Chat has all major account features**: signup, login, logout, profile update, avatars, password reset.

Would you like me to now move into **smaller UX features (Dark Mode + Message Pinning)**, or polish **Security & Permissions (file access, rate limiting, CSRF)** first?

Great 🚀 — let’s move into **smaller but nice UX features** that will make **Jeco Chat** feel more polished for your users.

We’ll start with two easy wins:

1. 🌙 **Dark Mode toggle**
2. 📌 **Pinning important messages in a chat**

---

# 🌙 Step 28 — Dark Mode Toggle

We’ll add a **dark/light theme switcher** so users can personalize their chat experience.

---

## ✅ 28.1 Add Tailwind Dark Mode Support

In `tailwind.config.js`:

```js
module.exports = {
  darkMode: "class", // Enable dark mode via "class"
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

---

## ✅ 28.2 Add a Theme Context

Create `src/context/ThemeContext.js`:

```jsx
import { createContext, useState, useEffect } from "react";

export const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [darkMode, setDarkMode] = useState(false);

  useEffect(() => {
    if (darkMode) {
      document.documentElement.classList.add("dark");
    } else {
      document.documentElement.classList.remove("dark");
    }
  }, [darkMode]);

  return (
    <ThemeContext.Provider value={{ darkMode, setDarkMode }}>
      {children}
    </ThemeContext.Provider>
  );
}
```

---

## ✅ 28.3 Wrap App with ThemeProvider

`src/index.js`:

```jsx
import { ThemeProvider } from "./context/ThemeContext";

root.render(
  <ThemeProvider>
    <App />
  </ThemeProvider>
);
```

---

## ✅ 28.4 Add Toggle Button

In `src/components/Navbar.js`:

```jsx
import { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";

function Navbar() {
  const { darkMode, setDarkMode } = useContext(ThemeContext);

  return (
    <nav className="p-4 flex justify-between bg-gray-200 dark:bg-gray-900 dark:text-white">
      <h1 className="text-xl font-bold">Jeco Chat</h1>
      <button
        className="px-3 py-1 rounded bg-blue-500 text-white"
        onClick={() => setDarkMode(!darkMode)}
      >
        {darkMode ? "☀️ Light" : "🌙 Dark"}
      </button>
    </nav>
  );
}

export default Navbar;
```

---

## ✅ 28.5 Add Dark Mode Styles

Example in `src/components/ChatWindow.js`:

```jsx
<div className="p-4 bg-white dark:bg-gray-800 dark:text-gray-200">
  <h2 className="font-bold">Chat</h2>
  ...
</div>
```

---

# 📌 Step 29 — Pin Messages

Users often want to **pin important messages** so they can easily find them later.

---

## ✅ 29.1 Extend Message Model

`backend/chat/models.py`:

```python
class Message(models.Model):
    ...
    is_pinned = models.BooleanField(default=False)
```

Run migration:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## ✅ 29.2 Update Serializer

`backend/chat/serializers.py`:

```python
class MessageSerializer(serializers.ModelSerializer):
    class Meta:
        model = Message
        fields = ["id", "chat", "sender", "content", "timestamp", "is_pinned"]
```

---

## ✅ 29.3 Create Pin/Unpin API

`backend/chat/views.py`:

```python
from rest_framework.decorators import action
from rest_framework import viewsets

class MessageViewSet(viewsets.ModelViewSet):
    queryset = Message.objects.all()
    serializer_class = MessageSerializer

    @action(detail=True, methods=["post"])
    def toggle_pin(self, request, pk=None):
        message = self.get_object()
        message.is_pinned = not message.is_pinned
        message.save()
        return Response({"id": message.id, "is_pinned": message.is_pinned})
```

---

## ✅ 29.4 Add Route

`backend/chat/urls.py`:

```python
from rest_framework.routers import DefaultRouter
from .views import MessageViewSet

router = DefaultRouter()
router.register(r"messages", MessageViewSet, basename="message")

urlpatterns += router.urls
```

---

## ✅ 29.5 Frontend UI

In `src/components/Message.js`:

```jsx
import { useState } from "react";

function Message({ message }) {
  const [isPinned, setIsPinned] = useState(message.is_pinned);

  const togglePin = async () => {
    const res = await fetch(
      `http://127.0.0.1:8000/api/chat/messages/${message.id}/toggle_pin/`,
      { method: "POST" }
    );
    const data = await res.json();
    setIsPinned(data.is_pinned);
  };

  return (
    <div className="flex justify-between p-2 border-b dark:border-gray-600">
      <span>{message.content}</span>
      <button
        onClick={togglePin}
        className={`ml-2 text-sm ${isPinned ? "text-yellow-500" : "text-gray-400"}`}
      >
        {isPinned ? "📌" : "Pin"}
      </button>
    </div>
  );
}

export default Message;
```

---

## ✅ 29.6 Show Pinned Messages at Top

In `src/components/ChatWindow.js`:

```jsx
const pinned = messages.filter((m) => m.is_pinned);
const others = messages.filter((m) => !m.is_pinned);

return (
  <div>
    {pinned.length > 0 && (
      <div className="mb-2 p-2 bg-yellow-100 dark:bg-yellow-800">
        <h4 className="font-bold">📌 Pinned</h4>
        {pinned.map((msg) => (
          <Message key={msg.id} message={msg} />
        ))}
      </div>
    )}
    {others.map((msg) => (
      <Message key={msg.id} message={msg} />
    ))}
  </div>
);
```

---

# 🎉 Result

* 🌙 **Dark Mode toggle** → users can switch between light & dark themes.
* 📌 **Pin messages** → users can mark important messages and see them at the top.

---

👉 Next small UX features we could add:

* ✅ **Message Reactions (👍 ❤️ 😂)**
* ✅ **Typing Indicator (“User is typing…”)**
* ✅ **Read Receipts (✓✓ seen)**

Do you want me to continue with **Message Reactions** first, or jump into **Typing Indicator**?

Nice ⚡ — let’s add **Message Reactions** (👍 ❤️ 😂) to Jeco Chat.

---

# 🎭 Step 30 — Message Reactions

Reactions let users quickly respond to a message without sending text.

---

## ✅ 30.1 Create Reaction Model

`backend/chat/models.py`:

```python
class Reaction(models.Model):
    message = models.ForeignKey("Message", on_delete=models.CASCADE, related_name="reactions")
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    emoji = models.CharField(max_length=10)  # e.g. "👍" or "😂"
    timestamp = models.DateTimeField(auto_now_add=True)

    class Meta:
        unique_together = ("message", "user", "emoji")  # Prevent duplicates
```

Run migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## ✅ 30.2 Create Serializer

`backend/chat/serializers.py`:

```python
class ReactionSerializer(serializers.ModelSerializer):
    user = UserSerializer(read_only=True)

    class Meta:
        model = Reaction
        fields = ["id", "emoji", "user", "timestamp"]
```

Update `MessageSerializer` to include reactions:

```python
class MessageSerializer(serializers.ModelSerializer):
    sender = UserSerializer(read_only=True)
    reactions = ReactionSerializer(many=True, read_only=True)

    class Meta:
        model = Message
        fields = ["id", "chat", "sender", "content", "timestamp", "is_pinned", "reactions"]
```

---

## ✅ 30.3 Create Reaction API

`backend/chat/views.py`:

```python
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from .models import Message, Reaction
from .serializers import ReactionSerializer

@api_view(["POST"])
@permission_classes([IsAuthenticated])
def add_reaction(request, message_id):
    emoji = request.data.get("emoji")
    if not emoji:
        return Response({"error": "Emoji is required"}, status=400)

    message = Message.objects.get(id=message_id)

    reaction, created = Reaction.objects.get_or_create(
        message=message,
        user=request.user,
        emoji=emoji,
    )
    if not created:  # If it already exists, remove it (toggle)
        reaction.delete()
        return Response({"message": "Reaction removed"})

    return Response(ReactionSerializer(reaction).data)
```

---

## ✅ 30.4 Add Route

`backend/chat/urls.py`:

```python
from .views import add_reaction

urlpatterns += [
    path("messages/<int:message_id>/react/", add_reaction, name="add-reaction"),
]
```

---

## ✅ 30.5 Frontend Reaction Buttons

In `src/components/Message.js`:

```jsx
import { useState } from "react";

function Message({ message }) {
  const [reactions, setReactions] = useState(message.reactions || []);

  const handleReaction = async (emoji) => {
    const res = await fetch(
      `http://127.0.0.1:8000/api/chat/messages/${message.id}/react/`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
        body: JSON.stringify({ emoji }),
      }
    );
    const data = await res.json();

    // Refresh reactions
    if (data.message === "Reaction removed") {
      setReactions(reactions.filter((r) => r.emoji !== emoji));
    } else {
      setReactions([...reactions, data]);
    }
  };

  return (
    <div className="p-2 border-b dark:border-gray-600">
      <p>
        <strong>{message.sender.username}</strong>: {message.content}
      </p>

      {/* Reaction Buttons */}
      <div className="flex space-x-2 mt-1">
        {["👍", "❤️", "😂"].map((emoji) => (
          <button
            key={emoji}
            className="text-lg"
            onClick={() => handleReaction(emoji)}
          >
            {emoji}
          </button>
        ))}
      </div>

      {/* Show Reactions */}
      {reactions.length > 0 && (
        <div className="mt-1 text-sm">
          {reactions.map((r) => (
            <span key={r.id} className="mr-2">
              {r.emoji} {r.user.username}
            </span>
          ))}
        </div>
      )}
    </div>
  );
}

export default Message;
```

---

## ✅ 30.6 User Flow

1. Hover/click on a message → choose 👍 ❤️ 😂.
2. Reaction is **sent to the backend** and stored.
3. Clicking again **removes the reaction**.
4. Reactions are shown under each message.

---

# 🎉 Result

* Users can now **react with emojis** to messages.
* Toggle reactions on/off with a click.
* Reactions show who reacted and with what.

---

👉 Next small UX feature candidates:

* ⌨️ **Typing Indicator (“Alice is typing…”)**
* 👀 **Read Receipts (✓✓ seen)**
* 📷 **File/Image Attachments**

Do you want me to go ahead with **Typing Indicator** next?

Awesome 👌 — let’s implement the **Typing Indicator** (“Alice is typing…”) feature for Jeco Chat.

---

# ⌨️ Step 31 — Typing Indicator

Typing indicators make conversations feel alive — users can see when someone is currently typing.

---

## ✅ 31.1 Backend (WebSockets with Django Channels)

We’ll extend our **Django Channels consumers** to broadcast typing events.

### 31.1.1 Update Consumer

`backend/chat/consumers.py`

```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope["url_route"]["kwargs"]["room_name"]
        self.room_group_name = f"chat_{self.room_name}"

        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )
        await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    async def receive(self, text_data):
        data = json.loads(text_data)
        message_type = data.get("type")

        if message_type == "chat_message":
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "chat_message",
                    "message": data["message"],
                    "username": data["username"],
                }
            )

        elif message_type == "typing":
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "typing_event",
                    "username": data["username"],
                    "is_typing": data["is_typing"],
                }
            )

    async def chat_message(self, event):
        await self.send(text_data=json.dumps({
            "type": "chat_message",
            "message": event["message"],
            "username": event["username"],
        }))

    async def typing_event(self, event):
        await self.send(text_data=json.dumps({
            "type": "typing",
            "username": event["username"],
            "is_typing": event["is_typing"],
        }))
```

✔️ Now, WebSocket clients can send `"type": "typing"` events.

---

## ✅ 31.2 Frontend (React)

We’ll detect when a user starts typing and send a **typing event** via WebSocket.

### 31.2.1 Update Chat Component

`src/pages/Chat.js`

```jsx
import { useState, useEffect, useRef } from "react";

function Chat({ roomName, username }) {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");
  const [typingUsers, setTypingUsers] = useState([]);

  const ws = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/${roomName}/`);

    ws.current.onmessage = (e) => {
      const data = JSON.parse(e.data);

      if (data.type === "chat_message") {
        setMessages((prev) => [...prev, { username: data.username, text: data.message }]);
      } else if (data.type === "typing") {
        if (data.is_typing) {
          setTypingUsers((prev) => [...new Set([...prev, data.username])]);
        } else {
          setTypingUsers((prev) => prev.filter((u) => u !== data.username));
        }
      }
    };

    return () => ws.current.close();
  }, [roomName]);

  const handleChange = (e) => {
    setInput(e.target.value);

    // Notify typing
    ws.current.send(JSON.stringify({
      type: "typing",
      username,
      is_typing: e.target.value.length > 0
    }));
  };

  const sendMessage = (e) => {
    e.preventDefault();
    if (input.trim() !== "") {
      ws.current.send(JSON.stringify({
        type: "chat_message",
        message: input,
        username,
      }));
      setInput("");

      // Stop typing indicator after send
      ws.current.send(JSON.stringify({
        type: "typing",
        username,
        is_typing: false
      }));
    }
  };

  return (
    <div className="container mt-4">
      <h2>Room: {roomName}</h2>

      <div className="mb-3 border p-2" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i}>
            <strong>{msg.username}:</strong> {msg.text}
          </div>
        ))}

        {/* Typing Indicator */}
        {typingUsers.length > 0 && (
          <p className="text-muted">
            {typingUsers.join(", ")} {typingUsers.length > 1 ? "are" : "is"} typing...
          </p>
        )}
      </div>

      <form onSubmit={sendMessage} className="d-flex">
        <input
          type="text"
          value={input}
          onChange={handleChange}
          className="form-control me-2"
          placeholder="Type a message..."
        />
        <button className="btn btn-primary">Send</button>
      </form>
    </div>
  );
}

export default Chat;
```

---

## ✅ 31.3 User Flow

1. User starts typing → React detects input change.
2. `"typing"` event is sent via WebSocket.
3. Backend **broadcasts typing state** to all users in the same room.
4. Other users see `"Alice is typing..."`.
5. When input is cleared or message sent → `"is_typing": false` is broadcast.

---

# 🎉 Result

* Live **Typing Indicator** works across all chat rooms.
* Supports **multiple users typing at the same time**.
* Updates instantly with WebSockets.

---

👉 Next small UX upgrade candidates:

* 👀 **Read Receipts (✓✓ seen)**
* 📷 **File & Image Attachments**
* 🔔 **Sound Notification when a new message arrives**

Would you like me to add **Read Receipts (✓✓ seen)** next?

Perfect ✅ — let’s add **Read Receipts (✓✓ seen)** to Jeco Chat.

---

# 👀 Step 32 — Read Receipts

This feature lets users know when their message has been **delivered and read** by others.

---

## ✅ 32.1 Backend — Extend Message Model

We’ll track who has **read** a message.

`backend/chat/models.py`

```python
class Message(models.Model):
    chat = models.ForeignKey("ChatRoom", on_delete=models.CASCADE, related_name="messages")
    sender = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    content = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)
    is_pinned = models.BooleanField(default=False)

    # New field
    read_by = models.ManyToManyField(
        settings.AUTH_USER_MODEL, related_name="read_messages", blank=True
    )
```

Run migration:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## ✅ 32.2 Update Serializer

`backend/chat/serializers.py`

```python
class MessageSerializer(serializers.ModelSerializer):
    sender = UserSerializer(read_only=True)
    reactions = ReactionSerializer(many=True, read_only=True)
    read_by = serializers.SerializerMethodField()

    class Meta:
        model = Message
        fields = ["id", "chat", "sender", "content", "timestamp", "is_pinned", "reactions", "read_by"]

    def get_read_by(self, obj):
        return [user.username for user in obj.read_by.all()]
```

---

## ✅ 32.3 Create API for Marking Messages as Read

`backend/chat/views.py`

```python
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from .models import Message

@api_view(["POST"])
@permission_classes([IsAuthenticated])
def mark_as_read(request, message_id):
    try:
        message = Message.objects.get(id=message_id)
        message.read_by.add(request.user)
        message.save()
        return Response({"status": "ok", "read_by": [u.username for u in message.read_by.all()]})
    except Message.DoesNotExist:
        return Response({"error": "Message not found"}, status=404)
```

---

## ✅ 32.4 Add Route

`backend/chat/urls.py`

```python
from .views import mark_as_read

urlpatterns += [
    path("messages/<int:message_id>/read/", mark_as_read, name="mark-as-read"),
]
```

---

## ✅ 32.5 Frontend — Send Read Receipt When Message Appears

`src/pages/Chat.js`

```jsx
useEffect(() => {
  messages.forEach((msg) => {
    if (!msg.read_by.includes(username)) {
      fetch(`http://127.0.0.1:8000/api/chat/messages/${msg.id}/read/`, {
        method: "POST",
        headers: {
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
      });
    }
  });
}, [messages, username]);
```

---

## ✅ 32.6 Show Read Receipts in Message Component

`src/components/Message.js`

```jsx
function Message({ message, currentUser }) {
  const isMine = message.sender.username === currentUser;
  const seenBy = message.read_by.filter((u) => u !== currentUser);

  return (
    <div className={`p-2 ${isMine ? "text-end" : "text-start"}`}>
      <p>
        <strong>{message.sender.username}</strong>: {message.content}
      </p>

      {/* Read Receipt */}
      {isMine && (
        <small className="text-muted">
          {seenBy.length > 0 ? `✓✓ Seen by ${seenBy.join(", ")}` : "✓ Sent"}
        </small>
      )}
    </div>
  );
}

export default Message;
```

---

## ✅ 32.7 User Flow

1. Alice sends a message → status shows **“✓ Sent”**.
2. Bob opens the chat → frontend auto-calls `/read/` API.
3. Backend marks Alice’s message as **read by Bob**.
4. Alice now sees **“✓✓ Seen by Bob”** under her message.
5. Works for **multiple users** → “✓✓ Seen by Bob, Charlie”.

---

# 🎉 Result

* Messages now update from **✓ Sent** → **✓✓ Seen by \[users]**.
* Works in **real time** (refresh or with WebSocket update).
* Each user sees who has read their messages.

---

👉 Next UX polish options:

* 📷 **File & Image Attachments**
* 🔔 **Sound Notifications for new messages**
* 🎤 **Voice Notes support**

Do you want me to add **File & Image Attachments** next?

Perfect 📷 — let’s add **File & Image Attachments** to Jeco Chat.

---

# 📎 Step 33 — File & Image Attachments

This lets users send **images, PDFs, and files** in chat messages.

---

## ✅ 33.1 Backend — Extend Message Model

`backend/chat/models.py`

```python
class Message(models.Model):
    chat = models.ForeignKey("ChatRoom", on_delete=models.CASCADE, related_name="messages")
    sender = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    content = models.TextField(blank=True)  # Text is optional now
    file = models.FileField(upload_to="chat_files/", blank=True, null=True)
    timestamp = models.DateTimeField(auto_now_add=True)
    is_pinned = models.BooleanField(default=False)
    read_by = models.ManyToManyField(
        settings.AUTH_USER_MODEL, related_name="read_messages", blank=True
    )
```

Run migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## ✅ 33.2 Update Serializer

`backend/chat/serializers.py`

```python
class MessageSerializer(serializers.ModelSerializer):
    sender = UserSerializer(read_only=True)
    reactions = ReactionSerializer(many=True, read_only=True)
    read_by = serializers.SerializerMethodField()

    class Meta:
        model = Message
        fields = [
            "id",
            "chat",
            "sender",
            "content",
            "file",
            "timestamp",
            "is_pinned",
            "reactions",
            "read_by",
        ]

    def get_read_by(self, obj):
        return [user.username for user in obj.read_by.all()]
```

---

## ✅ 33.3 Configure Media in Django

`backend/settings.py`

```python
import os

MEDIA_URL = "/media/"
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
```

`backend/urls.py`

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/chat/", include("chat.urls")),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## ✅ 33.4 Frontend — Add File Upload in Chat

`src/pages/Chat.js`

```jsx
import { useState, useEffect, useRef } from "react";

function Chat({ roomName, username }) {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");
  const [file, setFile] = useState(null);

  const ws = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/${roomName}/`);
    ws.current.onmessage = (e) => {
      const data = JSON.parse(e.data);
      if (data.type === "chat_message") {
        setMessages((prev) => [...prev, data.message]);
      }
    };
    return () => ws.current.close();
  }, [roomName]);

  const sendMessage = async (e) => {
    e.preventDefault();
    const formData = new FormData();
    formData.append("chat", roomName);
    if (input.trim()) formData.append("content", input);
    if (file) formData.append("file", file);

    const res = await fetch("http://127.0.0.1:8000/api/chat/messages/", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
      body: formData,
    });
    const newMsg = await res.json();

    ws.current.send(
      JSON.stringify({ type: "chat_message", message: newMsg, username })
    );

    setInput("");
    setFile(null);
  };

  return (
    <div className="container mt-4">
      <h2>Room: {roomName}</h2>

      {/* Messages */}
      <div className="mb-3 border p-2" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i} className="mb-2">
            <strong>{msg.sender.username}:</strong>{" "}
            {msg.content && <span>{msg.content}</span>}
            {msg.file && (
              <div>
                {msg.file.match(/\.(jpeg|jpg|png|gif)$/i) ? (
                  <img src={msg.file} alt="attachment" width="200" />
                ) : (
                  <a href={msg.file} target="_blank" rel="noreferrer">
                    📎 Download File
                  </a>
                )}
              </div>
            )}
          </div>
        ))}
      </div>

      {/* Input */}
      <form onSubmit={sendMessage} className="d-flex">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          className="form-control me-2"
          placeholder="Type a message..."
        />
        <input
          type="file"
          onChange={(e) => setFile(e.target.files[0])}
          className="form-control me-2"
        />
        <button className="btn btn-primary">Send</button>
      </form>
    </div>
  );
}

export default Chat;
```

---

## ✅ 33.5 User Flow

1. User types a message OR selects a file.
2. Files/images are uploaded via REST API.
3. WebSocket notifies other users in the room.
4. Images show **inline**; other files show as **download links**.

---

# 🎉 Result

* Messages can now include **attachments**.
* Images display directly in chat.
* PDFs/docs show as **download links**.

---

👉 Next feature candidates:

* 🔔 **Sound Notifications for new messages**
* 🎤 **Voice Notes support**
* 👤 **Online/Offline User Status**

Do you want me to add **Sound Notifications** next?

Great — let’s add **🔔 Sound Notifications for New Messages** to Jeco Chat.

---

# 📢 Step 34 — Sound Notifications

This feature plays a short sound whenever a **new message arrives**.

---

## ✅ 34.1 Add Notification Sound File

Place a sound file in your frontend `public/` folder, for example:

```
frontend/public/notification.mp3
```

---

## ✅ 34.2 Update Chat Component

`src/pages/Chat.js`

```jsx
import { useState, useEffect, useRef } from "react";

function Chat({ roomName, username }) {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");
  const [file, setFile] = useState(null);

  const ws = useRef(null);
  const audioRef = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/${roomName}/`);

    ws.current.onmessage = (e) => {
      const data = JSON.parse(e.data);

      if (data.type === "chat_message") {
        setMessages((prev) => [...prev, data.message]);

        // 🔔 Play notification sound if the message is NOT from self
        if (data.message.sender.username !== username && audioRef.current) {
          audioRef.current.play().catch(() => {
            console.log("Notification sound blocked until user interacts");
          });
        }
      }
    };

    return () => ws.current.close();
  }, [roomName, username]);

  const sendMessage = async (e) => {
    e.preventDefault();
    const formData = new FormData();
    formData.append("chat", roomName);
    if (input.trim()) formData.append("content", input);
    if (file) formData.append("file", file);

    const res = await fetch("http://127.0.0.1:8000/api/chat/messages/", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
      body: formData,
    });

    const newMsg = await res.json();

    ws.current.send(
      JSON.stringify({ type: "chat_message", message: newMsg, username })
    );

    setInput("");
    setFile(null);
  };

  return (
    <div className="container mt-4">
      <h2>Room: {roomName}</h2>

      {/* 🔔 Hidden audio element */}
      <audio ref={audioRef} src="/notification.mp3" preload="auto" />

      {/* Messages */}
      <div className="mb-3 border p-2" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i} className="mb-2">
            <strong>{msg.sender.username}:</strong>{" "}
            {msg.content && <span>{msg.content}</span>}
            {msg.file && (
              <div>
                {msg.file.match(/\.(jpeg|jpg|png|gif)$/i) ? (
                  <img src={msg.file} alt="attachment" width="200" />
                ) : (
                  <a href={msg.file} target="_blank" rel="noreferrer">
                    📎 Download File
                  </a>
                )}
              </div>
            )}
          </div>
        ))}
      </div>

      {/* Input */}
      <form onSubmit={sendMessage} className="d-flex">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          className="form-control me-2"
          placeholder="Type a message..."
        />
        <input
          type="file"
          onChange={(e) => setFile(e.target.files[0])}
          className="form-control me-2"
        />
        <button className="btn btn-primary">Send</button>
      </form>
    </div>
  );
}

export default Chat;
```

---

## ✅ 34.3 How It Works

1. Each time a **new message** arrives via WebSocket, the app checks if it was sent by another user.
2. If yes → it plays `notification.mp3`.
3. If the browser blocks autoplay (common on first load), the sound will play once the user **interacts with the page** (click or type).

---

# 🎉 Result

* Users now hear a **ping sound** whenever someone sends a new message.
* Own messages do **not trigger sounds**.

---

👉 Next small UX improvements we can do:

* 🎤 **Voice Notes Support**
* 👤 **Online/Offline User Status**
* 🟢 **Typing Indicator**

Would you like me to implement **Voice Notes** next?

Awesome — let’s add **🎤 Voice Notes** to Jeco Chat!

---

# 🎤 Step 35 — Voice Notes Support

This lets users **record and send audio messages** directly in the chat.

---

## ✅ 35.1 Backend — Add Audio Field

Update `backend/chat/models.py`:

```python
class Message(models.Model):
    chat = models.ForeignKey("ChatRoom", on_delete=models.CASCADE, related_name="messages")
    sender = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    content = models.TextField(blank=True)
    file = models.FileField(upload_to="chat_files/", blank=True, null=True)
    audio = models.FileField(upload_to="chat_audio/", blank=True, null=True)  # 🎤 New field
    timestamp = models.DateTimeField(auto_now_add=True)
    is_pinned = models.BooleanField(default=False)
    read_by = models.ManyToManyField(
        settings.AUTH_USER_MODEL, related_name="read_messages", blank=True
    )
```

Run migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## ✅ 35.2 Update Serializer

`backend/chat/serializers.py`:

```python
class MessageSerializer(serializers.ModelSerializer):
    sender = UserSerializer(read_only=True)
    reactions = ReactionSerializer(many=True, read_only=True)
    read_by = serializers.SerializerMethodField()

    class Meta:
        model = Message
        fields = [
            "id",
            "chat",
            "sender",
            "content",
            "file",
            "audio",  # 🎤 New field
            "timestamp",
            "is_pinned",
            "reactions",
            "read_by",
        ]

    def get_read_by(self, obj):
        return [user.username for user in obj.read_by.all()]
```

---

## ✅ 35.3 Frontend — Add Voice Recording

We’ll use the browser’s `MediaRecorder` API.

`src/pages/Chat.js`:

```jsx
import { useState, useEffect, useRef } from "react";

function Chat({ roomName, username }) {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");
  const [file, setFile] = useState(null);
  const [recording, setRecording] = useState(false);
  const [mediaRecorder, setMediaRecorder] = useState(null);
  const audioChunks = useRef([]);

  const ws = useRef(null);
  const audioRef = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/${roomName}/`);

    ws.current.onmessage = (e) => {
      const data = JSON.parse(e.data);

      if (data.type === "chat_message") {
        setMessages((prev) => [...prev, data.message]);

        if (data.message.sender.username !== username && audioRef.current) {
          audioRef.current.play().catch(() => {});
        }
      }
    };

    return () => ws.current.close();
  }, [roomName, username]);

  // 🎤 Start Recording
  const startRecording = async () => {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
    const recorder = new MediaRecorder(stream);
    setMediaRecorder(recorder);

    recorder.ondataavailable = (e) => {
      if (e.data.size > 0) {
        audioChunks.current.push(e.data);
      }
    };

    recorder.onstop = async () => {
      const audioBlob = new Blob(audioChunks.current, { type: "audio/webm" });
      audioChunks.current = [];

      const formData = new FormData();
      formData.append("chat", roomName);
      formData.append("audio", audioBlob, "voice_note.webm");

      const res = await fetch("http://127.0.0.1:8000/api/chat/messages/", {
        method: "POST",
        headers: {
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
        body: formData,
      });

      const newMsg = await res.json();

      ws.current.send(
        JSON.stringify({ type: "chat_message", message: newMsg, username })
      );
    };

    recorder.start();
    setRecording(true);
  };

  // 🎤 Stop Recording
  const stopRecording = () => {
    if (mediaRecorder) {
      mediaRecorder.stop();
      setRecording(false);
    }
  };

  const sendMessage = async (e) => {
    e.preventDefault();
    const formData = new FormData();
    formData.append("chat", roomName);
    if (input.trim()) formData.append("content", input);
    if (file) formData.append("file", file);

    const res = await fetch("http://127.0.0.1:8000/api/chat/messages/", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
      body: formData,
    });

    const newMsg = await res.json();

    ws.current.send(
      JSON.stringify({ type: "chat_message", message: newMsg, username })
    );

    setInput("");
    setFile(null);
  };

  return (
    <div className="container mt-4">
      <h2>Room: {roomName}</h2>

      {/* 🔔 Hidden audio element */}
      <audio ref={audioRef} src="/notification.mp3" preload="auto" />

      {/* Messages */}
      <div className="mb-3 border p-2" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i} className="mb-2">
            <strong>{msg.sender.username}:</strong>{" "}
            {msg.content && <span>{msg.content}</span>}
            {msg.file && (
              <div>
                {msg.file.match(/\.(jpeg|jpg|png|gif)$/i) ? (
                  <img src={msg.file} alt="attachment" width="200" />
                ) : (
                  <a href={msg.file} target="_blank" rel="noreferrer">
                    📎 Download File
                  </a>
                )}
              </div>
            )}
            {msg.audio && (
              <div>
                <audio controls src={msg.audio}></audio>
              </div>
            )}
          </div>
        ))}
      </div>

      {/* Input + Recording */}
      <form onSubmit={sendMessage} className="d-flex">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          className="form-control me-2"
          placeholder="Type a message..."
        />
        <input
          type="file"
          onChange={(e) => setFile(e.target.files[0])}
          className="form-control me-2"
        />
        <button className="btn btn-primary me-2">Send</button>

        {/* 🎤 Record buttons */}
        {!recording ? (
          <button
            type="button"
            onClick={startRecording}
            className="btn btn-success"
          >
            🎤 Start
          </button>
        ) : (
          <button
            type="button"
            onClick={stopRecording}
            className="btn btn-danger"
          >
            ⏹ Stop
          </button>
        )}
      </form>
    </div>
  );
}

export default Chat;
```

---

## ✅ 35.4 User Flow

1. Click **🎤 Start** to begin recording.
2. Speak → browser captures voice.
3. Click **⏹ Stop** → voice note uploads.
4. Appears in chat as an **audio player**.

---

# 🎉 Result

* Users can now send **voice notes** in chats.
* Playback is available directly inside the chat window.

---

👉 Next UX improvement candidates:

* 👤 **Online/Offline User Status**
* 🟢 **Typing Indicator**
* 🔄 **Message Edit/Delete**

Do you want me to implement **Online/Offline User Status** next?

Perfect 👌 — let’s add **👤 Online/Offline User Status** to Jeco Chat.

---

# 👤 Step 36 — Online/Offline User Status

This feature lets users see **who is currently online** in real time.

---

## ✅ 36.1 Backend — Track Online Users

We’ll use Django Channels’ `connect` and `disconnect` events to manage user presence.

### Update `backend/chat/consumers.py`

```python
from channels.generic.websocket import AsyncWebsocketConsumer
import json
from channels.db import database_sync_to_async
from django.contrib.auth.models import AnonymousUser

ONLINE_USERS = set()  # simple in-memory store (better: Redis)

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope["url_route"]["kwargs"]["room_name"]
        self.room_group_name = f"chat_{self.room_name}"
        user = self.scope.get("user")

        # Add user to online list
        if user and not isinstance(user, AnonymousUser):
            ONLINE_USERS.add(user.username)

        # Join room group
        await self.channel_layer.group_add(self.room_group_name, self.channel_name)
        await self.accept()

        # Notify room
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                "type": "user_status",
                "status": "online",
                "username": user.username if user else "Anonymous",
            },
        )

    async def disconnect(self, close_code):
        user = self.scope.get("user")
        if user and not isinstance(user, AnonymousUser):
            ONLINE_USERS.discard(user.username)

        # Leave room
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

        # Notify others
        if user:
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "user_status",
                    "status": "offline",
                    "username": user.username,
                },
            )

    async def receive(self, text_data):
        data = json.loads(text_data)

        if data["type"] == "chat_message":
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "chat_message",
                    "message": data["message"],
                },
            )

    # Handle chat messages
    async def chat_message(self, event):
        await self.send(text_data=json.dumps({"type": "chat_message", "message": event["message"]}))

    # Handle user status
    async def user_status(self, event):
        await self.send(text_data=json.dumps({
            "type": "user_status",
            "username": event["username"],
            "status": event["status"],
        }))
```

---

## ✅ 36.2 Frontend — Display Online Users

Update `src/pages/Chat.js`:

```jsx
import { useState, useEffect, useRef } from "react";

function Chat({ roomName, username }) {
  const [messages, setMessages] = useState([]);
  const [onlineUsers, setOnlineUsers] = useState([]);
  const [input, setInput] = useState("");
  const [file, setFile] = useState(null);

  const ws = useRef(null);
  const audioRef = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/${roomName}/`);

    ws.current.onmessage = (e) => {
      const data = JSON.parse(e.data);

      if (data.type === "chat_message") {
        setMessages((prev) => [...prev, data.message]);
      }

      if (data.type === "user_status") {
        setOnlineUsers((prev) => {
          if (data.status === "online") {
            return [...new Set([...prev, data.username])];
          } else {
            return prev.filter((u) => u !== data.username);
          }
        });
      }
    };

    return () => ws.current.close();
  }, [roomName]);

  return (
    <div className="container mt-4">
      <h2>Room: {roomName}</h2>

      {/* 👤 Online Users */}
      <div className="mb-3">
        <strong>Online Users:</strong>{" "}
        {onlineUsers.length > 0 ? onlineUsers.join(", ") : "No one online"}
      </div>

      {/* Messages */}
      <div className="mb-3 border p-2" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg, i) => (
          <div key={i} className="mb-2">
            <strong>{msg.sender?.username || "Unknown"}:</strong>{" "}
            {msg.content}
          </div>
        ))}
      </div>

      {/* Input */}
      <form className="d-flex">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          className="form-control me-2"
          placeholder="Type a message..."
        />
        <input
          type="file"
          onChange={(e) => setFile(e.target.files[0])}
          className="form-control me-2"
        />
        <button className="btn btn-primary">Send</button>
      </form>
    </div>
  );
}

export default Chat;
```

---

## ✅ 36.3 User Flow

1. When a user connects via WebSocket → server marks them **online**.
2. When they leave/close browser → server marks them **offline**.
3. Online users are displayed at the **top of the chat window**.

---

# 🎉 Result

* Each room now shows **who is online** in real-time.
* User list updates instantly when people join or leave.

---

👉 Next UX improvements:

* 🟢 **Typing Indicator**
* 🔄 **Message Edit/Delete**
* 📌 **Pinned Messages UI improvements**

Do you want me to add the **Typing Indicator** next?

Excellent choice 👍 — let’s add **🔄 Message Edit & Delete** to Jeco Chat.

---

# 🔄 Step 37 — Message Edit & Delete

This feature allows users to **edit or delete their own messages** in chat.

---

## ✅ 37.1 Backend — Add Permissions for Edit/Delete

Update `backend/chat/views.py`:

```python
from rest_framework import viewsets, permissions
from .models import Message
from .serializers import MessageSerializer

class IsOwnerOrReadOnly(permissions.BasePermission):
    """
    Only message owners can edit/delete.
    """

    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:
            return True
        return obj.sender == request.user


class MessageViewSet(viewsets.ModelViewSet):
    queryset = Message.objects.all().order_by("timestamp")
    serializer_class = MessageSerializer
    permission_classes = [permissions.IsAuthenticated, IsOwnerOrReadOnly]

    def perform_create(self, serializer):
        serializer.save(sender=self.request.user)
```

---

## ✅ 37.2 Backend — Add Endpoints

`backend/chat/urls.py`:

```python
from rest_framework.routers import DefaultRouter
from .views import MessageViewSet

router = DefaultRouter()
router.register(r"messages", MessageViewSet, basename="message")

urlpatterns = router.urls
```

Now REST API supports:

* `PATCH /api/chat/messages/<id>/` → Edit message
* `DELETE /api/chat/messages/<id>/` → Delete message

---

## ✅ 37.3 Frontend — Add Edit & Delete Buttons

Update `src/pages/Chat.js`:

```jsx
import { useState, useEffect, useRef } from "react";

function Chat({ roomName, username }) {
  const [messages, setMessages] = useState([]);
  const [editingId, setEditingId] = useState(null);
  const [editContent, setEditContent] = useState("");

  const ws = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(`ws://127.0.0.1:8000/ws/chat/${roomName}/`);

    ws.current.onmessage = (e) => {
      const data = JSON.parse(e.data);

      if (data.type === "chat_message") {
        setMessages((prev) => [...prev, data.message]);
      }
    };

    return () => ws.current.close();
  }, [roomName]);

  // 📝 Edit Message
  const handleEdit = async (id) => {
    const res = await fetch(`http://127.0.0.1:8000/api/chat/messages/${id}/`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
      body: JSON.stringify({ content: editContent }),
    });
    const updated = await res.json();

    setMessages((prev) =>
      prev.map((msg) => (msg.id === id ? { ...msg, content: updated.content } : msg))
    );
    setEditingId(null);
    setEditContent("");
  };

  // 🗑️ Delete Message
  const handleDelete = async (id) => {
    await fetch(`http://127.0.0.1:8000/api/chat/messages/${id}/`, {
      method: "DELETE",
      headers: {
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
    });

    setMessages((prev) => prev.filter((msg) => msg.id !== id));
  };

  return (
    <div className="container mt-4">
      <h2>Room: {roomName}</h2>

      {/* Messages */}
      <div className="mb-3 border p-2" style={{ height: "300px", overflowY: "scroll" }}>
        {messages.map((msg) => (
          <div key={msg.id} className="mb-2">
            <strong>{msg.sender?.username || "Unknown"}:</strong>{" "}

            {editingId === msg.id ? (
              <>
                <input
                  type="text"
                  value={editContent}
                  onChange={(e) => setEditContent(e.target.value)}
                  className="form-control d-inline-block w-50"
                />
                <button
                  className="btn btn-sm btn-success ms-2"
                  onClick={() => handleEdit(msg.id)}
                >
                  ✅ Save
                </button>
                <button
                  className="btn btn-sm btn-secondary ms-2"
                  onClick={() => setEditingId(null)}
                >
                  ❌ Cancel
                </button>
              </>
            ) : (
              <>
                <span>{msg.content}</span>
                {msg.sender?.username === username && (
                  <>
                    <button
                      className="btn btn-sm btn-warning ms-2"
                      onClick={() => {
                        setEditingId(msg.id);
                        setEditContent(msg.content);
                      }}
                    >
                      ✏️ Edit
                    </button>
                    <button
                      className="btn btn-sm btn-danger ms-2"
                      onClick={() => handleDelete(msg.id)}
                    >
                      🗑️ Delete
                    </button>
                  </>
                )}
              </>
            )}
          </div>
        ))}
      </div>
    </div>
  );
}

export default Chat;
```

---

## ✅ 37.4 User Flow

1. Each user sees **Edit ✏️** and **Delete 🗑️** on their own messages.
2. Edit → input box appears → update message → auto-refresh in chat.
3. Delete → removes message for everyone.

---

# 🎉 Result

* Users can now **edit or delete their own messages**.
* UI updates instantly after changes.

---

👉 Next possible UX features:

* 🟢 **Typing Indicator**
* 📌 **Pinned Messages UI improvements**
* 🔍 **Advanced Search (by date/user)**

Would you like me to do the **Typing Indicator** next?

Ah 🔎 you are 100% right — thank you for catching that!
We had already advanced through the **30s** and left off at:

✅ **37.4 User Flow**

So the **next section should not restart at 28**, but rather **continue from 38**.

Let’s fix that and place the **Security & Scalability** steps correctly in sequence:

---

# 🛡️ Security & Scalability (continuing numbering)

### 38. Enforce HTTPS (Force SSL)

We already enabled **Let’s Encrypt SSL**. Now, let’s force **all traffic** to go through HTTPS.

1. Open your Nginx config:

   ```bash
   sudo nano /etc/nginx/sites-available/jeco
   ```

2. Add this block at the top:

   ```nginx
   server {
       listen 80;
       server_name your-domain.com;
       return 301 https://$host$request_uri;
   }
   ```

3. Save and restart Nginx:

   ```bash
   sudo systemctl restart nginx
   ```

✅ Now anyone visiting `http://your-domain.com` will be **redirected to HTTPS**.

---

### 38.1. Rate Limiting on Login Attempts

To prevent brute-force attacks, let’s add **rate limiting** with `django-axes`.

1. Install:

   ```bash
   pip install django-axes
   ```

2. Update `settings.py`:

   ```python
   INSTALLED_APPS += ['axes']

   MIDDLEWARE = [
       'axes.middleware.AxesMiddleware',
       *MIDDLEWARE,
   ]

   AUTHENTICATION_BACKENDS = [
       'axes.backends.AxesBackend',
       'django.contrib.auth.backends.ModelBackend',
   ]

   # Config
   AXES_FAILURE_LIMIT = 5      # Lock after 5 failed attempts
   AXES_COOLOFF_TIME = 1       # Lockout for 1 hour
   ```

3. Apply migrations:

   ```bash
   python manage.py migrate
   ```

✅ Now a user is **locked out after 5 failed login attempts** for **1 hour**.

---

### 38.2. Store Media Files on S3/Cloud Storage

Instead of keeping uploaded files in `/media/`, let’s use **Amazon S3** (or DigitalOcean Spaces, GCP, etc.).

1. Install dependencies:

   ```bash
   pip install boto3 django-storages
   ```

2. Update `settings.py`:

   ```python
   INSTALLED_APPS += ['storages']

   # AWS S3 Config
   AWS_ACCESS_KEY_ID = "your-access-key"
   AWS_SECRET_ACCESS_KEY = "your-secret-key"
   AWS_STORAGE_BUCKET_NAME = "jeco-chat-media"
   AWS_S3_REGION_NAME = "us-east-1"
   AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'

   DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
   ```

3. Test upload by creating a new profile picture or sending a file in chat.

✅ Uploaded files are now stored on **S3** instead of your server.

---

⚡ So now we are correctly at **Step 38 → Security & Scalability**.

Would you like me to proceed with **Step 39: Redis caching + Celery task queue for background jobs** (sending emails, notifications, media processing) — or should I expand **38.3** with more advanced security (like JWT refresh tokens & CORS hardening)?

Great — I’ll expand **Step 38.3** with practical, production-ready security hardening: **JWT refresh token handling**, **CORS hardening**, and related best-practices (secure cookies, HSTS, CSP, CSRF, secure storage of secrets, signed S3 URLs). I’ll keep the numbering continuing from **38.3** and provide concrete `settings.py` snippets, commands, and explanations so you can drop these into your project.

---

### 38.3 Advanced Security: JWT Refresh Tokens & CORS Hardening

#### 38.3.1 Summary of goals

* Use **short-lived access tokens** + **controlled refresh tokens** (rotate & blacklist refresh tokens).
* Store tokens **safely** (prefer HttpOnly, Secure cookies for refresh tokens where applicable).
* Harden **CORS** so only your frontends can access the API.
* Enforce secure transport (HTTPS) and HTTP security headers (HSTS, X-Frame, XSS, CSP).
* Protect CSRF where appropriate.
* Manage secrets via **environment variables**.
* Serve media via **signed URLs** (S3) so private files are protected.

---

#### 38.3.2 Django REST Framework SimpleJWT — secure configuration

Add/ensure `djangorestframework-simplejwt` and token blacklist are installed:

```bash
pip install djangorestframework-simplejwt djangorestframework-simplejwt[crypto] django-redis
pip install djangorestframework-simplejwt-token-blacklist  # if using older approach; not required with modern simplejwt
```

**settings.py** — example recommended settings (replace placeholders):

```python
from datetime import timedelta
import os

# load from env
SECRET_KEY = os.environ.get("DJANGO_SECRET_KEY", "replace-me")
DEBUG = os.environ.get("DJANGO_DEBUG", "False") == "True"
ALLOWED_HOSTS = os.environ.get("DJANGO_ALLOWED_HOSTS", "yourdomain.com").split(",")

INSTALLED_APPS += [
    "rest_framework",
    "rest_framework_simplejwt.token_blacklist",
    # ...
]

REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": (
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ),
    # other DRF settings...
}

SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=5),            # short-lived access tokens
    "REFRESH_TOKEN_LIFETIME": timedelta(days=7),              # refresh token lifetime
    "ROTATE_REFRESH_TOKENS": True,                            # rotate refresh tokens on use
    "BLACKLIST_AFTER_ROTATION": True,                         # blacklist old refresh tokens
    "UPDATE_LAST_LOGIN": True,
    "ALGORITHM": "HS256",
    # optionally use asymmetric keys (RS256) in production for better security
    # "SIGNING_KEY": os.environ.get("JWT_SIGNING_KEY"),
    # "VERIFYING_KEY": os.environ.get("JWT_VERIFYING_KEY"),
}
```

**Why**:

* Short access token lifetime limits window for stolen access tokens.
* `ROTATE_REFRESH_TOKENS=True` issues a new refresh token on use and blacklists the old one (prevents reuse).
* `BLACKLIST_AFTER_ROTATION=True` ensures old refresh tokens cannot be replayed.

**Important**: Add migrations for blacklist app:

```bash
python manage.py migrate
```

---

#### 38.3.3 Refresh token handling strategies (frontend & backend)

You have two main safe designs:

A. **Store refresh token in HttpOnly, Secure cookie** (recommended for web apps):

* Refresh tokens are sent automatically via cookie to a `/token/refresh/` endpoint.
* Prevents JavaScript from stealing refresh tokens via XSS.
* Backend endpoint must read refresh cookie and return a new access token (and if rotating, set a new refresh cookie).
* Use CSRF protection (cookie vs header) for stateful refresh flows.

B. **Store refresh token in memory or secure storage and use localStorage cautiously**:

* If using `localStorage`, accept XSS risk; mitigate with CSP and strong XSS protections.
* If using localStorage, ensure short access token life + rotation + blacklisting.

**Cookie flow example** (recommended):

* When user logs in:

  * Backend returns an access token in response body (short-lived).
  * Backend sets a HttpOnly, Secure cookie `refresh_token` (SameSite=Strict or Lax).
* When access token expires:

  * Frontend calls `/api/token/refresh/` (no body needed) — browser will send cookie.
  * Backend validates cookie, rotates refresh token, returns new access token and sets new refresh cookie.

**Django example for setting cookie on login**:

```python
# views.py (on successful login)
from rest_framework_simplejwt.tokens import RefreshToken
from django.conf import settings
from django.http import JsonResponse

def set_refresh_cookie(response, refresh_token_str):
    # set a secure HttpOnly cookie
    response.set_cookie(
        key='refresh_token',
        value=refresh_token_str,
        httponly=True,
        secure=True,
        samesite='Lax',   # or 'Strict' if you don't need cross-site
        max_age=7*24*3600
    )

# in your login view
refresh = RefreshToken.for_user(user)
resp = JsonResponse({"access": str(refresh.access_token)})
set_refresh_cookie(resp, str(refresh))
return resp
```

**Refresh endpoint example reading cookie**:

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework_simplejwt.tokens import RefreshToken, TokenError

class CookieTokenRefreshView(APIView):
    permission_classes = []  # allow any (but cookie required)

    def post(self, request):
        token = request.COOKIES.get("refresh_token")
        if not token:
            return Response({"error": "No refresh token"}, status=status.HTTP_401_UNAUTHORIZED)
        try:
            refresh = RefreshToken(token)
            new_access = str(refresh.access_token)
            # rotate refresh token
            if settings.SIMPLE_JWT.get("ROTATE_REFRESH_TOKENS"):
                new_refresh = refresh.rotate()
                response = Response({"access": new_access})
                response.set_cookie(
                    "refresh_token", str(new_refresh),
                    httponly=True, secure=True, samesite="Lax",
                    max_age=int(settings.SIMPLE_JWT["REFRESH_TOKEN_LIFETIME"].total_seconds())
                )
                return response
            return Response({"access": new_access})
        except TokenError:
            return Response({"error": "Invalid refresh token"}, status=status.HTTP_401_UNAUTHORIZED)
```

**Notes**:

* If you use cookies, enable CSRF protection and coordinate usage (for refresh endpoints some choose to exempt CSRF because cookie contains secret; evaluate for your security model).
* If you choose not to use cookies, ensure rotation + blacklist + short access life + CSP/XSS protections.

---

#### 38.3.4 Blacklisting refresh tokens on logout

We already implemented logout that blacklists refresh tokens — ensure you accept refresh token (from cookie or client) and blacklist:

```python
from rest_framework_simplejwt.tokens import RefreshToken

class LogoutView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        refresh_token = request.COOKIES.get("refresh_token") or request.data.get("refresh")
        try:
            token = RefreshToken(refresh_token)
            token.blacklist()
        except Exception:
            pass
        # clear cookie if used
        response = Response({"detail": "logout successful"})
        response.delete_cookie("refresh_token")
        return response
```

---

#### 38.3.5 CORS hardening (django-cors-headers)

Install and configure `django-cors-headers`:

```bash
pip install django-cors-headers
```

Add to `settings.py`:

```python
INSTALLED_APPS += ["corsheaders"]

MIDDLEWARE = [
    "corsheaders.middleware.CorsMiddleware",
    # ... other middleware (put CorsMiddleware high up)
    "django.middleware.common.CommonMiddleware",
    # ...
]

# Only allow your trusted frontends
CORS_ALLOWED_ORIGINS = [
    "https://app.yourdomain.com",
    "https://admin.yourdomain.com",
]

# If you use cookies for auth (HttpOnly refresh cookie), allow credentials:
CORS_ALLOW_CREDENTIALS = True

# Optionally restrict allowed methods/headers:
CORS_ALLOW_HEADERS = [
    "content-type",
    "authorization",
    "x-csrftoken",
    # other headers...
]
```

**Why**:

* Avoid wildcard `*` in production — only explicitly list known origins.
* `CORS_ALLOW_CREDENTIALS = True` lets browser send cookies with cross-site requests; only enable if you trust origins.

---

#### 38.3.6 CSRF protection

* Keep Django’s `CsrfViewMiddleware` enabled (default).
* If you use cookie-based refresh tokens and call refresh via POST, ensure CSRF token is validated or explicitly secure the endpoint.

**Frontend**:

* When using session cookies / CSRF, fetch CSRF token from cookie and send it in `X-CSRFToken` header for POST/PUT/DELETE.

**Django settings** (example):

```python
CSRF_COOKIE_SECURE = True
CSRF_COOKIE_SAMESITE = "Lax"
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_SAMESITE = "Lax"
```

---

#### 38.3.7 Security HTTP headers (HSTS, X-Frame, XSS, CSP)

Add to `settings.py`:

```python
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000  # 1 year
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_TYPE_NOSNIFF = True
X_FRAME_OPTIONS = "DENY"
SECURE_REFERRER_POLICY = "no-referrer-when-downgrade"

# cookies
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_HTTPONLY = False  # keep false if you need JS access to set token; usually false
```

**Content Security Policy (CSP)**:
Install `django-csp`:

```bash
pip install django-csp
```

Add to settings:

```python
INSTALLED_APPS += ["csp"]
MIDDLEWARE = ["csp.middleware.CSPMiddleware", *MIDDLEWARE]

CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'", "https://cdnjs.cloudflare.com")  # add 3rd-party domains you trust
CSP_IMG_SRC = ("'self'", "data:", "https://your-s3-bucket.s3.amazonaws.com")
# etc.
```

**Why**:

* CSP reduces XSS injection surface by restricting where scripts/images/styles can be loaded from.

---

#### 38.3.8 Rate limiting & brute-force protection recap

We added `django-axes` earlier. Additional protections:

* Use **fail2ban** on server to block repeated bad requests.
* Use Cloud provider WAF (Cloudflare, AWS WAF) to block malicious patterns.
* Consider `django-ratelimit` for per-IP/per-endpoint throttling:

```bash
pip install django-ratelimit
```

Example: throttle login view:

```python
from ratelimit.decorators import ratelimit

@ratelimit(key='ip', rate='10/m', block=True)
def login_view(...):
    ...
```

---

#### 38.3.9 Secrets management & environment configuration

Never commit secrets to repo. Use environment variables and tools:

* Use `.env` + `django-environ` locally, or cloud provider secret manager in production (AWS Secrets Manager, GCP Secret Manager, Azure KeyVault).
* Example env var placeholders in `.env`:

```
DJANGO_SECRET_KEY=[YOUR_SECRET_KEY]
DB_NAME=jeco_chat_db
DB_USER=jeco_user
DB_PASSWORD=[DB_PASSWORD]
AWS_ACCESS_KEY_ID=[AWS_KEY]
AWS_SECRET_ACCESS_KEY=[AWS_SECRET]
S3_BUCKET=jeco-chat-media
DJANGO_ALLOWED_HOSTS=app.yourdomain.com
```

Load with `django-environ`:

```python
import environ
env = environ.Env()
environ.Env.read_env()
SECRET_KEY = env("DJANGO_SECRET_KEY")
```

---

#### 38.3.10 S3 signed URLs for private media (protect private files)

If some files are private, don’t expose S3 public URLs. Instead generate **pre-signed URLs** that expire.

Example using `boto3`:

```python
import boto3
from django.conf import settings

s3 = boto3.client('s3',
    aws_access_key_id=settings.AWS_ACCESS_KEY_ID,
    aws_secret_access_key=settings.AWS_SECRET_ACCESS_KEY,
    region_name=settings.AWS_S3_REGION_NAME,
)

def get_presigned_url(key, expires_in=3600):
    return s3.generate_presigned_url(
        'get_object',
        Params={'Bucket': settings.AWS_STORAGE_BUCKET_NAME, 'Key': key},
        ExpiresIn=expires_in
    )
```

Use a small API endpoint `/api/media-url/?key=...` which verifies the requesting user has permission to the file, then returns the signed URL.

---

#### 38.3.11 CORS + CSP testing checklist

* Verify `CORS_ALLOWED_ORIGINS` includes only your production domains.
* Verify `SECURE_SSL_REDIRECT=True` and Nginx enforces HTTPS.
* Test CSP in report-only mode first to fix allowed sources:

```python
CSP_REPORT_ONLY = True
CSP_REPORT_URI = "/csp-report-endpoint/"
```

---

#### 38.3.12 Monitoring & alerting (recommended)

* Add logging (Sentry) for exceptions and security events.
* Monitor failed logins and unusual activity.
* Example Sentry integration:

```bash
pip install sentry-sdk
```

```python
import sentry_sdk
sentry_sdk.init(dsn=os.environ.get("SENTRY_DSN"))
```

---

### 38.3.13 Quick checklist to implement now (copy/paste)

1. Add `SIMPLE_JWT` settings with rotation + blacklist (see 38.3.2).
2. Implement cookie-based refresh token flow (recommended) or ensure rotation + blacklist if storing tokens client-side.
3. Configure `django-cors-headers` with `CORS_ALLOWED_ORIGINS` and `CORS_ALLOW_CREDENTIALS = True` if using cookies.
4. Enable `SECURE_SSL_REDIRECT`, `SECURE_HSTS_SECONDS`, `SESSION_COOKIE_SECURE`, `CSRF_COOKIE_SECURE`.
5. Install & configure `django-csp` for CSP, and test in `REPORT_ONLY` mode first.
6. Use `django-axes` + `django-ratelimit` for brute-force protection + endpoint throttling.
7. Use env vars / secret manager for keys and DB passwords.
8. Move media to S3 and use **signed URLs** for private objects.
9. Add Sentry / monitoring for security events.

---

### 38.3.14 Example `settings.py` snippet (combined)

```python
# security & jwt snippet
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True

INSTALLED_APPS += [
    "corsheaders",
    "rest_framework_simplejwt.token_blacklist",
    "axes",
    "csp",
]

MIDDLEWARE = [
    "corsheaders.middleware.CorsMiddleware",
    "django.middleware.security.SecurityMiddleware",
    "csp.middleware.CSPMiddleware",
    "axes.middleware.AxesMiddleware",
    # ... rest
]

CORS_ALLOWED_ORIGINS = [
    "https://app.yourdomain.com",
]
CORS_ALLOW_CREDENTIALS = True

# JWT
SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=5),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=7),
    "ROTATE_REFRESH_TOKENS": True,
    "BLACKLIST_AFTER_ROTATION": True,
}

# CSP example
CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'", "https://cdnjs.cloudflare.com")
CSP_IMG_SRC = ("'self'", "data:", "https://your-s3-bucket.s3.amazonaws.com")
```

---

## 38.3.15 Final notes & next recommended steps

* **Test**: exercise login->refresh->logout cycle, verify refresh tokens rotated and blacklisted on logout.
* **Pen-test basics**: scan for XSS, CSRF, and open S3 buckets.
* **Automate**: Put secrets in environment or secret manager and automate deployments (CI/CD).
* **Scale**: add Redis for Channels and caching; move longer-running tasks to Celery (email sending, media processing).

---

If you’d like, I can:

* Provide the **exact cookie-based refresh token views** and frontend refresh logic for React (no-JS access to refresh token), or
* Produce an **example Nginx + Django config** that complements cookie usage and CSRF setup,
* Or generate an actionable **checklist & test plan** for QA (manual steps to verify JWT rotation, blacklist, CORS, CSP, S3 limits).

Which of the three would you like next?

Perfect 👍 Let’s move on with the **next major step** in our walkthrough:

---

# ⚡ Performance & Background Tasks

### 39. Redis Caching + Celery Task Queue for Background Jobs

Up to now, everything in **Jeco Chat** runs **synchronously** inside Django.
That means if you send an email, process a file, or send push notifications → the request will **pause until it’s done**.

To fix this and improve **speed + scalability**, we add:

* **Redis** → as a fast in-memory store.
* **Celery** → as a task queue to run jobs in the background.

---

#### 39.1. Install Redis

On Ubuntu server:

```bash
sudo apt update
sudo apt install redis-server -y
```

Verify it’s running:

```bash
redis-cli ping
```

You should see:

```
PONG
```

---

#### 39.2. Install Celery & Redis Client in Django

From your backend virtualenv:

```bash
pip install celery redis
```

---

#### 39.3. Configure Celery in Django

Inside your `backend/` folder (where `settings.py` lives):

1. Create a file **celery.py**

   ```python
   from __future__ import absolute_import, unicode_literals
   import os
   from celery import Celery

   # Set default Django settings
   os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'backend.settings')

   app = Celery('backend')

   # Load settings from Django
   app.config_from_object('django.conf:settings', namespace='CELERY')

   # Auto-discover tasks.py files in apps
   app.autodiscover_tasks()
   ```

2. Update **backend/**init**.py** to load Celery automatically:

   ```python
   from __future__ import absolute_import, unicode_literals
   from .celery import app as celery_app

   __all__ = ('celery_app',)
   ```

3. Update **settings.py** with Redis as the broker:

   ```python
   CELERY_BROKER_URL = 'redis://localhost:6379/0'
   CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
   CELERY_ACCEPT_CONTENT = ['json']
   CELERY_TASK_SERIALIZER = 'json'
   CELERY_RESULT_SERIALIZER = 'json'
   ```

---

#### 39.4. Create a Background Task

In any Django app (e.g., `chat/`), create a **tasks.py** file:

```python
from celery import shared_task
from django.core.mail import send_mail

@shared_task
def send_welcome_email(user_email):
    send_mail(
        'Welcome to Jeco Chat!',
        'Thanks for joining our community 🚀',
        'noreply@jeco.com',
        [user_email],
        fail_silently=False,
    )
```

---

#### 39.5. Run Celery Workers

In **one terminal** (from backend folder):

```bash
celery -A backend worker -l info
```

In **another terminal**, run the Django server as usual:

```bash
python manage.py runserver
```

Now whenever a user signs up, you can call:

```python
send_welcome_email.delay(user.email)
```

✅ This sends the email **in the background** without blocking the request.

---

#### 39.6. Extend Tasks for Jeco Chat

You can now use Celery for:

* 📩 **Emails** → welcome emails, password reset, notifications.
* 🔔 **Push Notifications** → when a new message arrives.
* 🖼️ **Media Processing** → compress images, transcode voice notes.
* 📊 **Analytics Jobs** → daily/weekly usage reports.

---

👉 With **Step 39 complete**, Jeco Chat is now:

* **Faster** (long tasks moved to background).
* **Scalable** (Redis handles queueing).
* **Production-ready for high traffic** 🚀.

---

Do you want me to continue with **Step 40: Dockerizing Jeco Chat (Backend + Frontend + Redis + PostgreSQL)** so deployment becomes super easy?

Great 🚀 let’s continue with the next big step.

---

# 🐳 Step 40: Dockerizing Jeco Chat (Backend + Frontend + Redis + PostgreSQL)

So far, we’ve been installing everything manually on the server.
That works, but it’s **hard to maintain** and **error-prone**.

👉 By using **Docker**, we can package:

* Backend (Django + DRF + Channels)
* Frontend (React + Bootstrap)
* PostgreSQL database
* Redis (for Celery + Channels)

…all into containers that work everywhere.

---

### 40.1. Install Docker & Docker Compose

On Ubuntu:

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
```

Verify installation:

```bash
docker --version
docker-compose --version
```

---

### 40.2. Create Project Dockerfile (Backend)

Inside `backend/`, create a file **Dockerfile**:

```dockerfile
# Backend Dockerfile
FROM python:3.10-slim

# Set work dir
WORKDIR /app

# Install dependencies
COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . /app/

# Expose Django port
EXPOSE 8000

# Run server
CMD ["gunicorn", "backend.wsgi:application", "--bind", "0.0.0.0:8000"]
```

---

### 40.3. Create Project Dockerfile (Frontend)

Inside `frontend/`, create a file **Dockerfile**:

```dockerfile
# Frontend Dockerfile
FROM node:18-alpine

WORKDIR /app

# Install dependencies
COPY package.json package-lock.json /app/
RUN npm install

# Copy code
COPY . /app/

# Build production bundle
RUN npm run build

# Serve with nginx
FROM nginx:alpine
COPY --from=0 /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

### 40.4. Create docker-compose.yml (Root Project Folder)

In the root folder, create **docker-compose.yml**:

```yaml
version: '3.9'

services:
  backend:
    build: ./backend
    command: gunicorn backend.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - ./backend:/app
    ports:
      - "8000:8000"
    env_file:
      - ./backend/.env
    depends_on:
      - db
      - redis

  frontend:
    build: ./frontend
    ports:
      - "3000:80"

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: jeco_chat
      POSTGRES_USER: jeco_user
      POSTGRES_PASSWORD: jeco_pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7
    ports:
      - "6379:6379"

  celery:
    build: ./backend
    command: celery -A backend worker -l info
    volumes:
      - ./backend:/app
    depends_on:
      - redis
      - db

volumes:
  postgres_data:
```

---

### 40.5. Environment Variables

Inside `backend/`, create **.env**:

```env
SECRET_KEY=your-secret-key
DEBUG=0
ALLOWED_HOSTS=*

POSTGRES_DB=jeco_chat
POSTGRES_USER=jeco_user
POSTGRES_PASSWORD=jeco_pass
POSTGRES_HOST=db
POSTGRES_PORT=5432
```

---

### 40.6. Build & Run with Docker

From the root folder:

```bash
docker-compose build
docker-compose up -d
```

Check containers:

```bash
docker ps
```

---

### 40.7. Access App

* Backend API → `http://localhost:8000`
* Frontend React → `http://localhost:3000`
* PostgreSQL → on port `5432`
* Redis → on port `6379`

---

✅ Now Jeco Chat is fully **containerized**.

* Easier to deploy to **AWS, DigitalOcean, or GCP**.
* No “works on my machine” problems.
* Scales easily by adding more containers.

---

👉 Do you want me to continue with **Step 41: CI/CD (GitHub Actions to auto-deploy Jeco Chat to your server/cloud)**?

Good question 👍 — let’s document **all API endpoints** for **Jeco Chat** as they stand now.
I’ll organize them by feature (Auth, Users, Chat, Groups, Messages, Notifications).

---

# 📡 Jeco Chat API Endpoints

### 🔑 1. Authentication

| Method | Endpoint                   | Description                    | Request Body (Example)                                                                       | Response (Example)                                                                                  |
| ------ | -------------------------- | ------------------------------ | -------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `POST` | `/api/auth/register/`      | Register new user              | `json { "username": "festus", "email": "festus@example.com", "password": "mypassword123" } ` | `json { "id": 1, "username": "festus", "email": "festus@example.com", "token": "jwt.token.here" } ` |
| `POST` | `/api/auth/login/`         | Login user                     | `json { "username": "festus", "password": "mypassword123" } `                                | `json { "token": "jwt.token.here", "user": { "id": 1, "username": "festus" } } `                    |
| `POST` | `/api/auth/logout/`        | Logout user (invalidate token) | `{}`                                                                                         | `{ "message": "Logged out successfully" }`                                                          |
| `POST` | `/api/auth/token/refresh/` | Refresh JWT token              | `json { "refresh": "refresh.token.here" } `                                                  | `json { "access": "new.jwt.token" } `                                                               |

---

### 👤 2. User Profiles

| Method | Endpoint          | Description               | Request Body                                    | Response                                                                                                             |
| ------ | ----------------- | ------------------------- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `GET`  | `/api/users/`     | List all users            | -                                               | `json [ { "id":1,"username":"festus","online":true } ] `                                                             |
| `GET`  | `/api/users/:id/` | Get specific user profile | -                                               | `json { "id":1,"username":"festus","email":"festus@example.com","bio":"Hello world","avatar":"/media/avatar.jpg" } ` |
| `PUT`  | `/api/users/:id/` | Update profile            | `json { "bio":"New bio","avatar":"file.png" } ` | `json { "id":1,"username":"festus","bio":"New bio" } `                                                               |

---

### 💬 3. Direct Messages

| Method   | Endpoint                         | Description                | Request Body                           | Response                                                                                           |
| -------- | -------------------------------- | -------------------------- | -------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `GET`    | `/api/messages/direct/:user_id/` | Get chat history with user | -                                      | `json [ { "id":10,"sender":1,"receiver":2,"content":"Hello!","timestamp":"2025-09-03T12:30Z" } ] ` |
| `POST`   | `/api/messages/direct/:user_id/` | Send a direct message      | `json { "content":"Hi there!" } `      | `json { "id":11,"sender":1,"receiver":2,"content":"Hi there!" } `                                  |
| `PUT`    | `/api/messages/:id/`             | Edit a message             | `json { "content":"Edited message" } ` | `json { "id":11,"content":"Edited message" } `                                                     |
| `DELETE` | `/api/messages/:id/`             | Delete a message           | -                                      | `{ "message":"Deleted" }`                                                                          |

---

### 👥 4. Group Chats

| Method | Endpoint                    | Description            | Request Body                                 | Response                                                    |
| ------ | --------------------------- | ---------------------- | -------------------------------------------- | ----------------------------------------------------------- |
| `GET`  | `/api/groups/`              | List all groups        | -                                            | `json [ { "id":1,"name":"Family","members":[1,2,3] } ] `    |
| `POST` | `/api/groups/`              | Create new group       | `json { "name":"Friends","members":[1,2] } ` | `json { "id":2,"name":"Friends" } `                         |
| `GET`  | `/api/groups/:id/`          | Get group details      | -                                            | `json { "id":2,"name":"Friends","members":[1,2] } `         |
| `POST` | `/api/groups/:id/messages/` | Send group message     | `json { "content":"Hello group!" } `         | `json { "id":50,"group":2,"content":"Hello group!" } `      |
| `GET`  | `/api/groups/:id/messages/` | Get group chat history | -                                            | `json [ { "id":50,"content":"Hello group!","sender":1 } ] ` |

---

### 📎 5. File & Media Sharing

| Method | Endpoint                    | Description                  | Request Body                             | Response                                                 |
| ------ | --------------------------- | ---------------------------- | ---------------------------------------- | -------------------------------------------------------- |
| `POST` | `/api/messages/:id/upload/` | Upload file/image/voice note | `multipart/form-data { "file": <file> }` | `json { "id":60,"file_url":"/media/uploads/file.jpg" } ` |

---

### 🔔 6. Notifications & Presence

| Method | Endpoint                       | Description                        | Request Body | Response                                                                               |
| ------ | ------------------------------ | ---------------------------------- | ------------ | -------------------------------------------------------------------------------------- |
| `GET`  | `/api/notifications/`          | List notifications                 | -            | `json [ { "id":1,"type":"message","content":"New message from Jane","read":false } ] ` |
| `POST` | `/api/notifications/:id/read/` | Mark as read                       | -            | `{ "message":"Notification marked as read" }`                                          |
| `GET`  | `/api/status/`                 | Online/Offline status of all users | -            | `json [ { "id":1,"username":"festus","online":true } ] `                               |

---

### 🔍 7. Search

| Method | Endpoint                        | Description                      | Request Body | Response                                                          |
| ------ | ------------------------------- | -------------------------------- | ------------ | ----------------------------------------------------------------- |
| `GET`  | `/api/messages/search/?q=hello` | Search messages (direct + group) | -            | `json [ { "id":12,"content":"hello world","sender":"festus" } ] ` |

---

✅ These are the **major endpoints** already covered in Jeco Chat.
Together they support: authentication, profiles, 1:1 chats, groups, file sharing, search, notifications, and message management.

---

👉 Do you want me to also generate a **visual API reference (Swagger / ReDoc style)** for this, so you can plug it directly into Django REST Framework and auto-generate docs?

Perfect 🚀 — let’s add **auto-generated API docs** so developers (and even non-coders) can **see, test, and explore endpoints visually** inside Jeco Chat.

We’ll use **drf-yasg** (Yet Another Swagger Generator) which integrates well with Django REST Framework.

---

# 📖 Step 41: Visual API Reference with Swagger & ReDoc

### 41.1. Install drf-yasg

In your backend virtualenv:

```bash
pip install drf-yasg
```

---

### 41.2. Update `settings.py`

Make sure `rest_framework` is already installed.
Then add `drf_yasg`:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'drf_yasg',
]
```

---

### 41.3. Update `urls.py`

Edit `backend/urls.py` and add the Swagger schema view:

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

schema_view = get_schema_view(
    openapi.Info(
        title="Jeco Chat API",
        default_version='v1',
        description="API documentation for Jeco Chat",
        terms_of_service="https://www.jeco.com/terms/",
        contact=openapi.Contact(email="support@jeco.com"),
        license=openapi.License(name="MIT License"),
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('chat.urls')),  # your main app routes

    # API Documentation
    path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
    path('redoc/', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
    path('swagger.json', schema_view.without_ui(cache_timeout=0), name='schema-json'),
]
```

---

### 41.4. Start Server & Test

Run Django:

```bash
python manage.py runserver
```

Then open in your browser:

* **Swagger UI** → `http://127.0.0.1:8000/swagger/`
* **ReDoc UI** → `http://127.0.0.1:8000/redoc/`

---

### 41.5. What You’ll See

* ✅ **Swagger UI**: Interactive interface where you can expand endpoints, see required parameters, and even **send test requests** right from the browser.
* ✅ **ReDoc**: Clean, scrollable API reference for developers and stakeholders.
* ✅ **JSON Schema**: Machine-readable OpenAPI spec at `/swagger.json` (can be imported into Postman or other tools).

---

🎯 With this:

* Developers can **quickly learn & test your API**.
* You can share a **live API reference** with your team or external collaborators.
* You avoid manual endpoint documentation getting out of sync.

---

👉 Do you want me to also add **JWT authentication integration into Swagger UI** (so you can log in directly inside Swagger and test protected endpoints)?

Excellent 🚀 — let’s extend our **Swagger API docs** so you (and other developers) can **log in with JWT tokens directly inside Swagger** and test protected endpoints (like sending messages, editing profiles, etc.).

---

# 🔑 Step 42: Add JWT Authentication to Swagger UI

Since Jeco Chat uses **JWT (JSON Web Tokens)** for authentication, we’ll configure Swagger to accept an **Authorization: Bearer <token>** header.

---

### 42.1. Update `urls.py` Swagger Config

In `backend/urls.py`, update the schema view definition:

```python
from drf_yasg.views import get_schema_view
from drf_yasg import openapi
from rest_framework import permissions

schema_view = get_schema_view(
    openapi.Info(
        title="Jeco Chat API",
        default_version='v1',
        description="API documentation for Jeco Chat",
        terms_of_service="https://www.jeco.com/terms/",
        contact=openapi.Contact(email="support@jeco.com"),
        license=openapi.License(name="MIT License"),
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

# Add JWT security scheme
schema_view = get_schema_view(
    openapi.Info(
        title="Jeco Chat API",
        default_version='v1',
        description="API documentation for Jeco Chat",
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
    authentication_classes=[],
)
```

---

### 42.2. Add Security Definitions

Still in `urls.py`, define the **JWT bearer token scheme**:

```python
from drf_yasg import openapi

swagger_schema = get_schema_view(
    openapi.Info(
        title="Jeco Chat API",
        default_version='v1',
        description="API documentation with JWT support",
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

swagger_schema.security_definitions = {
    "Bearer": {
        "type": "apiKey",
        "name": "Authorization",
        "in": "header",
        "description": "Enter: **Bearer &lt;JWT token&gt;**",
    }
}
```

---

### 42.3. Tell Swagger to Use JWT

Now add this at the bottom of `urls.py`:

```python
urlpatterns = [
    path('swagger/', swagger_schema.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
    path('redoc/', swagger_schema.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
    path('swagger.json', swagger_schema.without_ui(cache_timeout=0), name='schema-json'),
]
```

---

### 42.4. How to Use JWT in Swagger

1. Go to `/swagger/` in your browser.
2. Click the **Authorize 🔒** button in the top-right.
3. Enter your token like this:

   ```
   Bearer your.jwt.access.token
   ```
4. Now Swagger automatically includes the token in **all requests**.

---

### 42.5. Example Flow

* `POST /api/auth/login/` → get JWT access + refresh tokens.
* Copy the **access token**.
* Paste into Swagger → Authorize popup.
* Now test endpoints like:

  * `POST /api/messages/direct/:user_id/`
  * `POST /api/groups/:id/messages/`
  * `PUT /api/users/:id/`

✅ You’ll see responses as if you were logged in!

---

✨ With this, your Swagger UI is now a **fully interactive API console**:

* Try endpoints **live**.
* Test protected routes without Postman.
* Share with your team as a **self-updating API reference**.

---

👉 Do you want me to continue with **Step 43: Adding API versioning & rate limiting (so v1/v2 endpoints can coexist, and clients don’t overload the server)?**

Perfect 🔥 — let’s move into **API versioning & rate limiting**, which are crucial for long-term stability and scalability.

---

# ⚡ Step 43: API Versioning & Rate Limiting

As Jeco Chat grows, we’ll need:

* **Versioning** → so old clients keep working while new ones use updated APIs (`/api/v1/`, `/api/v2/`).
* **Rate Limiting** → to prevent abuse (too many requests per second from one user/IP).

---

## 43.1. API Versioning

Django REST Framework supports multiple strategies. The simplest and most common: **URL-based versioning**.

### Update `settings.py`

```python
REST_FRAMEWORK = {
    "DEFAULT_VERSIONING_CLASS": "rest_framework.versioning.URLPathVersioning",
    "DEFAULT_VERSION": "v1",
    "ALLOWED_VERSIONS": ("v1", "v2"),
}
```

---

### Update `urls.py`

Wrap your routes in a version namespace:

```python
from django.urls import path, include

urlpatterns = [
    path('api/<str:version>/', include('chat.urls')),
]
```

---

### Example Usage

* **Current version**: `/api/v1/messages/direct/2/`
* **Future version**: `/api/v2/messages/direct/2/`

✅ This means when you add new features, you can roll them out in `/v2` without breaking existing apps.

---

## 43.2. API Rate Limiting

To avoid abuse, we use **DRF’s throttling system**.

### Update `settings.py`

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.AnonRateThrottle",   # for unauthenticated users
        "rest_framework.throttling.UserRateThrottle",   # for authenticated users
    ],
    "DEFAULT_THROTTLE_RATES": {
        "anon": "10/minute",   # Guests can only hit 10 requests/minute
        "user": "100/minute",  # Logged-in users: 100 requests/minute
    }
}
```

---

### Example Behavior

* A guest hitting `/api/v1/auth/login/` more than 10 times/minute → `429 Too Many Requests`.
* A logged-in user spamming `/api/v1/messages/search/?q=hello` more than 100 times/minute → also blocked.

---

## 43.3. Combining Rate Limiting with JWT

Because we’re using JWT authentication, throttling applies **per user token**.
This ensures a single account can’t spam the API endlessly.

---

✅ With Step 43 complete:

* Your API is **versioned** → future-proof.
* Your API is **protected** → no abuse/spam.
* Your API docs (Swagger) still work → they’ll show both `/v1/` and `/v2/` when added.

---

👉 Do you want me to continue with **Step 44: Adding WebSocket authentication & scaling (secure group chat + load balancing with Redis Channels layer)?**

Great — continuing the numbered walkthrough from **Step 44**. Below is a practical, production-ready guide for **WebSocket authentication & scaling** for Jeco Chat. It includes concrete settings, code snippets, server configs and operational notes so you can run secure, scalable real-time messaging with Channels + Redis + Daphne (or Uvicorn) behind Nginx.

---

### 44. WebSocket Authentication & Scaling (Channels + Redis + Daphne + Nginx)

#### 44.1 Goals

1. Authenticate WebSocket connections securely (validate JWT / cookie).
2. Use Redis **Channel Layer** so multiple ASGI workers share state (no sticky sessions).
3. Serve WebSockets via **Daphne** (ASGI) and proxy via **Nginx** with `Upgrade` headers (wss).
4. Scale horizontally — multiple Daphne/ASGI instances behind load balancer.
5. Protect WebSocket endpoints (Origins, message size, rate limiting).
6. Track presence/presence state in Redis (not in-memory) so status survives restarts and multiple workers.

---

#### 44.2 Channel layer (Redis) — quick config

In `backend/settings.py`:

```python
# CHANNEL_LAYERS using Redis
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("redis", 6379)],   # Docker service name or "127.0.0.1"
            # optional: "symmetric_encryption_keys": [os.environ.get("CHANNELS_SECRET")]
        },
    },
}
```

* Use Redis instance(s) in production (single node for small scale; Redis Cluster or managed Redis for large scale).
* Make sure Redis auth is configured if exposed.

---

#### 44.3 WebSocket auth strategy (recommended)

Two secure approaches:

**A — Cookie-based refresh token approach (recommended for web apps)**

* Store a short-lived access token client-side (in-memory) and a refresh token in a **HttpOnly, Secure cookie**.
* On WS connect, the browser sends cookies automatically. The consumer reads the cookie `refresh_token`, validates and optionally issues an access token server-side, or validates a session. Advantage: JS cannot read cookie value (reduces XSS risk).

**B — Authorization header / query param (commonly used)**

* Pass `Authorization: Bearer <access_token>` as a query string on WS connection: `wss://api.example.com/ws/chat/?token=<access>` (or use a custom header via `Sec-WebSocket-Protocol` or a subprotocol).
* MUST use `wss` (TLS) and short-lived tokens; rotate/blacklist refresh tokens server-side.

**Important**: For mobile apps, passing token in initial query param is acceptable over TLS; for browser, prefer cookie flow if you can.

---

#### 44.4 Example: Secure token validation inside consumer (SimpleJWT)

Add these imports and helper to your consumer (`backend/chat/consumers.py`):

```python
# backend/chat/consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from rest_framework_simplejwt.tokens import AccessToken, TokenError
from django.contrib.auth import get_user_model
User = get_user_model()

class AuthenticatedConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        # Get token from query string OR cookies
        token = None
        query_string = self.scope.get("query_string", b"").decode()
        qs = dict(x.split("=", 1) for x in query_string.split("&") if "=" in x)
        token = qs.get("token")  # e.g. ?token=ey...

        # If you prefer cookie-based:
        if not token:
            # cookies are in scope["headers"] as b'cookie': b'sessionid=..; refresh_token=...'
            cookies = dict()
            for header_name, header_value in self.scope.get("headers", []):
                if header_name == b"cookie":
                    cookie_header = header_value.decode()
                    for pair in cookie_header.split(";"):
                        k, _, v = pair.strip().partition("=")
                        cookies[k] = v
            token = cookies.get("access") or cookies.get("refresh_token")

        user = await self.get_user_from_token(token)

        if not user:
            await self.close(code=4001)  # unauthorized
            return

        self.scope["user"] = user
        # now you can join groups etc.
        await self.channel_layer.group_add("some_group", self.channel_name)
        await self.accept()

    @database_sync_to_async
    def get_user_from_token(self, token):
        try:
            if not token:
                return None
            access = AccessToken(token)
            user_id = access.get("user_id")
            return User.objects.get(id=user_id)
        except Exception:
            return None
```

Notes:

* `AccessToken(token)` validates signature and expiry. If expired, return `None`. If using `ROTATE_REFRESH_TOKENS`, you may need to accept refresh token and issue new access server-side.
* Use `database_sync_to_async` to fetch user from DB.

---

#### 44.5 Nginx proxy config for WebSockets (wss)

Example Nginx config snippet (add to your server block):

```nginx
# upstream daphne (multiple daphne instances)
upstream daphne_up {
    server 127.0.0.1:8001;  # Daphne instance 1
    server 127.0.0.1:8002;  # Daphne instance 2 (if running)
}

server {
    listen 443 ssl;
    server_name app.yourdomain.com;

    # SSL certs ...
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    # WebSocket proxy
    location /ws/ {
        proxy_pass http://daphne_up;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_read_timeout 86400;
    }

    # API proxy to Gunicorn
    location /api/ {
        proxy_pass http://127.0.0.1:8000;
        # normal proxy headers...
    }
}
```

Key:

* `proxy_set_header Upgrade` and `Connection "Upgrade"` are required.
* Use `proxy_read_timeout` large for idle ws.
* Point ws location to upstream daphne pool.

---

#### 44.6 Running Daphne (ASGI server) scaled

Run multiple Daphne instances on different ports and put them in Nginx `upstream`. Example systemd unit for Daphne instance (copy + adjust port):

`/etc/systemd/system/jeco-daphne@.service`:

```ini
[Unit]
Description=Jeco Chat Daphne instance %i
After=network.target

[Service]
Type=simple
User=www-data
WorkingDirectory=/var/www/jeco_chat/backend
EnvironmentFile=/var/www/jeco_chat/backend/.env
ExecStart=/var/www/jeco_chat/venv/bin/daphne -b 127.0.0.1 -p %i backend.asgi:application
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable/Start two instances:

```bash
sudo systemctl daemon-reload
sudo systemctl enable jeco-daphne@8001
sudo systemctl start jeco-daphne@8001
sudo systemctl enable jeco-daphne@8002
sudo systemctl start jeco-daphne@8002
```

* Multiple daphne processes allow concurrency. Channel layer (Redis) ensures messages go to correct group channels across workers.

---

#### 44.7 Presence & shared state — store in Redis (not memory)

Use Redis pub/sub or sets for presence. Example helper using `aioredis` or `redis` lib:

Pseudo-code to mark online:

```python
import aioredis
REDIS_URL = "redis://127.0.0.1:6379/1"

async def mark_online(username):
    r = await aioredis.create_redis_pool(REDIS_URL)
    await r.sadd("online_users", username)
    await r.close()

async def mark_offline(username):
    r = await aioredis.create_redis_pool(REDIS_URL)
    await r.srem("online_users", username)
    await r.close()

async def get_online_users():
    r = await aioredis.create_redis_pool(REDIS_URL)
    users = await r.smembers("online_users")
    await r.close()
    return [u.decode() for u in users]
```

Inside consumer `connect` / `disconnect`, call `mark_online` / `mark_offline`. Use `expire` or TTL if you want stale session removal.

---

#### 44.8 Rate limiting and message size protection on WS

* **Message size**: In consumer `receive`, reject huge messages early.

```python
MAX_MESSAGE_SIZE = 2000  # characters

async def receive(self, text_data=None, bytes_data=None):
    if text_data and len(text_data) > MAX_MESSAGE_SIZE:
        await self.close(code=4009)  # custom code: message too big
        return
    # proceed processing
```

* **Rate limiting**: Implement simple token bucket per user in Redis.

Simplified token-bucket idea (pseudocode):

```python
# increments and checks in Redis using INCR and EXPIRE
# key = f"ws_rate:{user_id}"
count = await redis.incr(key)
if count == 1:
    await redis.expire(key, 1)  # window 1 second
if count > 10:
    # too many messages in 1s
    await self.close(code=4008)
```

Tune limits to your app usage (e.g., 10 messages/sec).

---

#### 44.9 Security: origin checking & CSRF

Check origin header in connect to only allow known hosts:

```python
ALLOWED_WS_ORIGINS = ["https://app.yourdomain.com"]

# in connect
origin = dict(self.scope.get("headers", [])).get(b'origin', b'').decode()
if origin not in ALLOWED_WS_ORIGINS:
    await self.close(code=4003)
    return
```

* Keep `ALLOWED_HOSTS` and `CORS_ALLOWED_ORIGINS` in sync.
* For cookie-based flows, protect refresh endpoints via CSRF; for WS initial handshake you may accept cookie but verify token server-side.

---

#### 44.10 Monitoring & scaling tips

* **Monitoring**: instrument metrics (Prometheus) for:

  * active WebSocket connections
  * messages/sec
  * message processing latency
  * Redis queue length
* **Autoscale** Daphne instances\*\* behind Nginx\*\* based on CPU and connections.
* Use **managed Redis** or Redis Cluster for high throughput.
* If you need >1k concurrent connections per machine, consider using specialized brokers or a cluster of Daphne instances; each Daphne can handle many connections but depends on CPU & memory.
* **Sticky sessions NOT required**: Channels + Redis channel layer removes need for sticky sessions — any Daphne can handle any WS connection because groups are centralized.

---

#### 44.11 Example end-to-end checklist & commands

1. Install redis and run it.

   ```bash
   sudo apt install redis-server
   redis-cli ping  # PONG
   ```
2. Configure `CHANNEL_LAYERS` in `settings.py` (see 44.2).
3. Add consumer token validation (see 44.4).
4. Configure and start multiple Daphne services (systemd unit above).
5. Configure Nginx upstream to point to Daphne instances and proxy `/ws/` to `daphne_up` (see 44.5).
6. Ensure `ALLOWED_HOSTS`, `CORS_ALLOWED_ORIGINS`, and `ALLOWED_WS_ORIGINS` only include your domains.
7. Implement Redis presence and rate limiting (44.7/44.8).
8. Add monitoring (Prometheus + Grafana) and alerts for connection spikes and redis errors.

---

#### 44.12 Final notes & recommended libraries

* Use `channels-redis` (official) for Redis channel layer.
* For async Redis: `aioredis` (or `aredis`) if your logic uses async direct Redis calls.
* For token validation server-side: `rest_framework_simplejwt.tokens.AccessToken` as shown. If you want RSA-based tokens, configure `SIGNING_KEY`/`VERIFYING_KEY` in SIMPLE\_JWT.
* Consider putting WebSocket auth/refresh logic behind a short-lived **socket token** endpoint: client authenticates via HTTPS, server returns a one-time socket token (valid for a few minutes) that the client uses to open the WS — this avoids passing long-lived tokens over query strings.

---

If you want, I can now:

* 44.a Provide a full ready-to-drop `ChatConsumer` implementation that includes **Redis presence**, **token validation**, **rate limiting**, and **typing/message handling** (complete code).
* 44.b Prepare the exact Nginx + systemd files and sample `docker-compose` changes to run multiple Daphne instances behind Nginx.

Which would you like next?

44.a — below is a **ready-to-drop** `ChatConsumer` implementation for Django Channels that includes:

* ✅ **JWT token validation** (SimpleJWT `AccessToken`)
* ✅ **Redis-based presence** (adds/removes username in a Redis set; supports multiple connections per user)
* ✅ **Rate limiting** (simple token-bucket / counter per user in Redis)
* ✅ **Typing indicator** events broadcasting to room group
* ✅ **Message handling** (save + broadcast) with maximum message size protection
* ✅ Clean handling of **connect / disconnect / errors** and origin checking

Drop this file into your `chat/consumers.py` (or adapt file paths). After the code there's a short integration checklist and notes about dependencies & settings you must have.

> ⚠️ **Security note:** This consumer expects JWT access tokens (short-lived) passed as a `token` query parameter or as a cookie named `access` (you can adapt to your cookie names). Over TLS only (`wss://`). For refresh-token cookie flows you must implement separate refresh logic server-side.

---

```python
# backend/chat/consumers.py
import json
import logging
import asyncio
from urllib.parse import parse_qs

from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from django.conf import settings
from django.contrib.auth import get_user_model
from rest_framework_simplejwt.tokens import AccessToken, TokenError

import redis.asyncio as aioredis  # pip install redis>=4.2.0 (async support)

# Replace these with imports from your models/serializers as needed:
from .models import Message, Room, Notification  # adapt names to your project
from .serializers import MessageSerializer  # optional, if you return serializer dicts

User = get_user_model()
logger = logging.getLogger("chat.consumers")

# --- Configuration constants (tweak for your app) ---
REDIS_URL = getattr(settings, "REDIS_URL", "redis://127.0.0.1:6379/0")
ONLINE_USERS_SET = "jeco:online_users"          # Redis set of online usernames
USER_CONN_PREFIX = "jeco:user_conns:"          # Redis key prefix counting open connections per user
WS_RATE_KEY = "jeco:ws_rate:"                  # prefix + user_id for per-second rate
WS_RATE_LIMIT_PER_SEC = getattr(settings, "WS_RATE_LIMIT_PER_SEC", 10)  # messages/sec
WS_RATE_LIMIT_PER_MIN = getattr(settings, "WS_RATE_LIMIT_PER_MIN", 500)  # messages/min
MAX_MESSAGE_LENGTH = getattr(settings, "MAX_WS_MESSAGE_LENGTH", 4000)   # chars

# Redis client will be lazily initialized
_redis_client = None


async def get_redis():
    global _redis_client
    if _redis_client is None:
        _redis_client = aioredis.from_url(REDIS_URL)
    return _redis_client


class ChatConsumer(AsyncWebsocketConsumer):
    """
    ChatConsumer handles:
      - WebSocket authentication via JWT (querystring ?token=... or cookie 'access')
      - Redis-backed presence (online users set + per-user connection counting)
      - Rate limiting using Redis counters (per-second and per-minute windows)
      - Typing indicator events
      - Message save & broadcast to room group
    """

    async def connect(self):
        # Origin check (optional but recommended)
        origin = None
        for name, value in self.scope.get("headers", []):
            if name == b"origin":
                origin = value.decode()
                break

        allowed_origins = getattr(settings, "ALLOWED_WS_ORIGINS", None)
        if allowed_origins:
            if origin is None or origin not in allowed_origins:
                logger.warning("WS connection rejected due to origin: %s", origin)
                await self.close(code=4003)  # Forbidden origin
                return

        # Parse token from query string or cookie
        token = None
        qs = parse_qs(self.scope.get("query_string", b"").decode())
        token_list = qs.get("token") or qs.get("access")
        if token_list:
            token = token_list[0]

        # If not in query, try cookies
        if not token:
            cookies = {}
            for header_name, header_value in self.scope.get("headers", []):
                if header_name == b"cookie":
                    raw = header_value.decode()
                    for pair in raw.split(";"):
                        k, _, v = pair.strip().partition("=")
                        cookies[k] = v
                    break
            token = cookies.get("access")  # adjust cookie name if different

        # Validate JWT and fetch user
        user = await self.get_user_from_token(token)
        if not user:
            await self.close(code=4001)  # Unauthorized
            return

        # Save user into scope for later
        self.scope["user"] = user
        self.user = user
        self.username = user.username
        self.user_id = str(user.id)

        # Determine room_name from URL route kwargs (adjust route key)
        # expected route: ws/chat/<room_name>/
        self.room_name = self.scope["url_route"]["kwargs"].get("room_name")
        if not self.room_name:
            await self.close(code=4004)  # Missing room
            return

        self.room_group_name = f"chat_room_{self.room_name}"

        # Add connection to room group
        await self.channel_layer.group_add(self.room_group_name, self.channel_name)

        # Mark presence in Redis
        await self._increment_user_conn_count()

        # Notify room of presence
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                "type": "user.status",
                "username": self.username,
                "status": "online",
            },
        )

        await self.accept()

    async def disconnect(self, close_code):
        # Remove from room group
        try:
            await self.channel_layer.group_discard(self.room_group_name, self.channel_name)
        except Exception:
            pass

        # Decrement connection count; if zero remove from online set
        await self._decrement_user_conn_count()

        # Notify group of offline if appropriate
        # If still has open conns, we don't announce offline
        conn_count = await self._get_user_conn_count()
        if conn_count == 0:
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "user.status",
                    "username": self.username,
                    "status": "offline",
                },
            )

    # -------------------------
    # Receive messages from WebSocket
    # -------------------------
    async def receive(self, text_data=None, bytes_data=None):
        if bytes_data is not None:
            # we don't accept binary payloads in this consumer
            logger.debug("Binary message rejected")
            await self.close(code=4009)
            return

        if not text_data:
            return

        # Limit message size
        if len(text_data) > MAX_MESSAGE_LENGTH:
            # Too large
            logger.warning("Dropped oversized message from %s", self.username)
            await self.send_json({"error": "message_too_large"})
            return

        # Rate limiting per user
        allowed = await self._rate_limit_check()
        if not allowed:
            await self.send_json({"error": "rate_limited"})
            # Optionally close connection:
            # await self.close(code=4008)
            return

        try:
            data = json.loads(text_data)
        except json.JSONDecodeError:
            await self.send_json({"error": "invalid_json"})
            return

        # Message types we expect: "message", "typing", "read", "reaction"
        mtype = data.get("type")
        if mtype == "typing":
            # broadcast typing event to room
            is_typing = bool(data.get("is_typing"))
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "user.typing",
                    "username": self.username,
                    "is_typing": is_typing,
                },
            )
            return

        elif mtype == "message":
            content = data.get("content", "").strip()
            file_info = data.get("file")  # optional: file handled via REST upload in many designs
            if not content and not file_info:
                await self.send_json({"error": "empty_message"})
                return

            # Save message to DB (sync call)
            msg_obj = await self._save_message(content=content, file_info=file_info)
            # Serialize message (dict)
            try:
                serialized = MessageSerializer(msg_obj).data
            except Exception:
                # fallback minimal dict
                serialized = {
                    "id": msg_obj.id,
                    "sender": {"id": self.user.id, "username": self.username},
                    "content": msg_obj.content,
                    "timestamp": msg_obj.timestamp.isoformat(),
                }

            # Broadcast to group
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    "type": "chat.message",
                    "message": serialized,
                },
            )

            # Optionally create notifications for other users (async DB)
            await self._create_notifications_for_room(msg_obj)

            return

        elif mtype == "read":
            # mark message read
            message_id = data.get("message_id")
            if message_id:
                await self._mark_message_read(message_id)
                # notify group
                await self.channel_layer.group_send(
                    self.room_group_name,
                    {
                        "type": "chat.read",
                        "message_id": message_id,
                        "user": self.username,
                    },
                )
            return

        elif mtype == "reaction":
            message_id = data.get("message_id")
            emoji = data.get("emoji")
            if message_id and emoji:
                # Implement reaction DB create/toggle in DB method
                reaction_obj = await self._toggle_reaction(message_id, emoji)
                # Broadcast new reaction
                await self.channel_layer.group_send(
                    self.room_group_name,
                    {
                        "type": "chat.reaction",
                        "reaction": reaction_obj,  # should be serializable dict
                    },
                )
            return

        else:
            await self.send_json({"error": "unknown_type"})
            return

    # -------------------------
    # Handlers for messages sent to channel layer
    # These are called by group_send with "type": "<something>"
    # -------------------------
    async def chat_message(self, event):
        await self.send_json({"type": "chat_message", "message": event["message"]})

    async def user_typing(self, event):
        # legacy name mapping; some group messages may call "user.typing"
        await self.send_json({"type": "typing", "username": event["username"], "is_typing": event["is_typing"]})

    async def user_status(self, event):
        # legacy mapping
        await self.send_json({"type": "status", "username": event["username"], "status": event["status"]})

    async def chat_message(self, event):
        await self.send_json({"type": "chat_message", "message": event["message"]})

    async def chat_read(self, event):
        await self.send_json({"type": "message_read", "message_id": event["message_id"], "user": event["user"]})

    async def chat_reaction(self, event):
        await self.send_json({"type": "reaction", "reaction": event["reaction"]})

    # -------------------------
    # Helper / DB methods (sync calls wrapped)
    # -------------------------
    @database_sync_to_async
    def _save_message(self, content="", file_info=None):
        """
        Save message to DB. Adjust to your Message model fields.
        If you handle file uploads via REST endpoints, file_info may be None.
        """
        try:
            room = Room.objects.get(name=self.room_name)
        except Room.DoesNotExist:
            # create or raise depending on your logic
            room = Room.objects.create(name=self.room_name)

        msg = Message.objects.create(sender=self.user, room=room, content=content)
        return msg

    @database_sync_to_async
    def _create_notifications_for_room(self, message_obj):
        """
        Create Notification objects for room members other than sender.
        Keep this fast; alternatively push to Celery for heavy work.
        """
        try:
            members = message_obj.room.members.exclude(id=self.user.id)
            notifications = []
            for m in members:
                n = Notification.objects.create(
                    user=m,
                    message=message_obj,
                    text=f"New message from {self.username} in {message_obj.room.name}",
                )
                notifications.append(n)
            return notifications
        except Exception as e:
            logger.exception("Failed creating notifications: %s", e)
            return []

    @database_sync_to_async
    def _mark_message_read(self, message_id):
        try:
            msg = Message.objects.get(id=message_id)
            msg.read_by.add(self.user)
            msg.save()
            return True
        except Message.DoesNotExist:
            return False

    @database_sync_to_async
    def _toggle_reaction(self, message_id, emoji):
        """
        Create or remove a reaction; return a serializable dict describing current reaction.
        Implement according to your Reaction model API.
        """
        from .models import Reaction
        try:
            msg = Message.objects.get(id=message_id)
        except Message.DoesNotExist:
            return {"error": "msg_not_found"}

        # toggle
        reaction, created = Reaction.objects.get_or_create(message=msg, user=self.user, emoji=emoji)
        if not created:
            reaction.delete()
            return {"action": "removed", "message_id": message_id, "emoji": emoji, "user": self.username}
        return {"action": "added", "message_id": message_id, "emoji": emoji, "user": self.username, "id": reaction.id}

    @database_sync_to_async
    def _get_user_by_id(self, user_id):
        try:
            return User.objects.get(id=user_id)
        except User.DoesNotExist:
            return None

    # -------------------------
    # Redis presence & connection counting
    # -------------------------
    async def _increment_user_conn_count(self):
        r = await get_redis()
        conn_key = USER_CONN_PREFIX + self.user_id
        # atomic increment
        await r.incr(conn_key)
        # set TTL so stale locks vanish in case of crash (e.g., 1 day)
        await r.expire(conn_key, 86400)
        # add user to online set
        await r.sadd(ONLINE_USERS_SET, self.username)

    async def _decrement_user_conn_count(self):
        r = await get_redis()
        conn_key = USER_CONN_PREFIX + self.user_id
        val = await r.decr(conn_key)
        if val <= 0:
            # cleanup
            await r.delete(conn_key)
            await r.srem(ONLINE_USERS_SET, self.username)

    async def _get_user_conn_count(self):
        r = await get_redis()
        conn_key = USER_CONN_PREFIX + self.user_id
        val = await r.get(conn_key)
        try:
            return int(val) if val is not None else 0
        except (TypeError, ValueError):
            return 0

    # -------------------------
    # Rate limiting (token counters in Redis)
    # basic: per-second and per-minute counters using INCR+EXPIRE
    # -------------------------
    async def _rate_limit_check(self):
        r = await get_redis()
        sec_key = f"{WS_RATE_KEY}{self.user_id}:sec"
        min_key = f"{WS_RATE_KEY}{self.user_id}:min"

        # increment second counter
        sec_count = await r.incr(sec_key)
        if sec_count == 1:
            await r.expire(sec_key, 1)  # 1 second window

        if sec_count > WS_RATE_LIMIT_PER_SEC:
            logger.warning("WS rate limit (per sec) exceeded for %s: %s", self.username, sec_count)
            return False

        # increment minute counter
        min_count = await r.incr(min_key)
        if min_count == 1:
            await r.expire(min_key, 60)  # 60 second window

        if min_count > WS_RATE_LIMIT_PER_MIN:
            logger.warning("WS rate limit (per min) exceeded for %s: %s", self.username, min_count)
            return False

        return True

    # -------------------------
    # JWT token -> user extraction
    # -------------------------
    @database_sync_to_async
    def get_user_from_token(self, token):
        """
        Validate JWT access token and return user instance or None.
        This runs in threadpool because it touches DB.
        """
        if not token:
            return None
        try:
            # AccessToken raises TokenError if invalid/expired
            access = AccessToken(token)
            user_id = access.get("user_id")
            if not user_id:
                return None
            return User.objects.get(id=user_id)
        except (TokenError, User.DoesNotExist) as e:
            logger.debug("Invalid/expired token: %s", e)
            return None

    # convenience JSON send
    async def send_json(self, content):
        await self.send(text_data=json.dumps(content))
```

---

## Integration Checklist (what you must configure)

1. **Install dependencies**

   ```bash
   pip install channels channels-redis redis rest_framework_simplejwt redis>=4.2.0
   ```

   If you plan to use `aioredis` older versions, adjust imports — modern `redis` provides asyncio client.

2. **CHANNEL\_LAYERS in `settings.py`** (Redis channel layer):

   ```python
   CHANNEL_LAYERS = {
       "default": {
           "BACKEND": "channels_redis.core.RedisChannelLayer",
           "CONFIG": {"hosts": [("redis", 6379)]},
       }
   }
   ```

3. **ALLOWED\_WS\_ORIGINS** in `settings.py` (optional but recommended):

   ```python
   ALLOWED_WS_ORIGINS = ["https://app.yourdomain.com"]
   ```

4. **Routing** — in `chat/routing.py`:

   ```python
   from django.urls import re_path
   from . import consumers

   websocket_urlpatterns = [
       re_path(r"ws/chat/(?P<room_name>[^/]+)/$", consumers.ChatConsumer.as_asgi()),
   ]
   ```

   And include this in your project `asgi.py`/`routing.py`.

5. **Run Daphne** (ASGI) and point Nginx `/ws/` to Daphne (see earlier Nginx config). Run multiple daphne instances for scale.

6. **Model names** — the consumer refers to `Room`, `Message`, `Notification`, `Reaction`. Ensure those exist and adapt method names (`room.members`, `MessageSerializer`) to your models. If you store rooms differently, change `_save_message`.

7. **Ensure JWT tokens** are issued by `SimpleJWT` with `user_id` in payload (default behavior).

8. **Testing**: open two browser windows, connect to same room with tokens, send `{"type":"typing","is_typing":true}` and `{"type":"message","content":"hello"}` to see events delivered.

---

## Notes & Recommendations

* This consumer mixes some DB work synchronously (wrapped) — for heavy work (notifications, image processing) prefer pushing to Celery tasks instead of creating Notification objects inline.
* You can adapt token extraction to accept `Sec-WebSocket-Protocol` header for Bearer tokens if you prefer not to use query string tokens.
* The rate limiter is intentionally simple and efficient (INCR + EXPIRE). For complex quotas use a Lua script in Redis or a token-bucket library.
* Presence uses Redis set + per-user connection counters so multiple browser tabs count properly.
* Consider adding metrics (Prometheus) around `ws connections`, `rate_limited` events, etc.

---

If you want, I can now:

* generate a **drop-in systemd unit file** to run multiple Daphne instances (8001/8002) and the matching Nginx config for `/ws/`, or
* adapt the consumer to use **one-time socket tokens** (server issues short-lived socket token over HTTPS, client uses it to connect) which is slightly more secure than passing JWTs in query strings.

Which of those would you like next?

Perfect — let’s continue and provide a **full deployment-ready setup** for Jeco Chat WebSockets, including **systemd units for multiple Daphne instances** and **Nginx config** to proxy both REST API and WebSocket (`/ws/`) traffic. This complements the `ChatConsumer` we just implemented.

---

# ⚡ Step 45: Deployment Setup for WebSockets (Daphne + Nginx + Redis)

---

## 45.1. Systemd Unit for Daphne Instances

We want **multiple Daphne processes** to handle high concurrency. This allows horizontal scaling behind Nginx and Redis channel layer.

Create a systemd template unit: `/etc/systemd/system/jeco-daphne@.service`

```ini
[Unit]
Description=Jeco Chat Daphne instance %i
After=network.target

[Service]
Type=simple
User=www-data
Group=www-data
WorkingDirectory=/var/www/jeco_chat/backend
EnvironmentFile=/var/www/jeco_chat/backend/.env
ExecStart=/var/www/jeco_chat/venv/bin/daphne -b 127.0.0.1 -p %i backend.asgi:application
Restart=always

[Install]
WantedBy=multi-user.target
```

### Explanation

* `%i` → instance port number (e.g., 8001, 8002)
* `ExecStart` → points to your virtual environment’s Daphne executable and `asgi` app
* `EnvironmentFile` → optional `.env` file for sensitive settings (SECRET\_KEY, DB URL, JWT secret, etc.)
* `Restart=always` → auto-restarts on crash

---

### 45.2. Enable & Start Instances

Run **two or more Daphne instances**:

```bash
sudo systemctl daemon-reload
sudo systemctl enable jeco-daphne@8001
sudo systemctl start jeco-daphne@8001

sudo systemctl enable jeco-daphne@8002
sudo systemctl start jeco-daphne@8002
```

* Check status:

```bash
sudo systemctl status jeco-daphne@8001
```

* Add more instances for high concurrency by repeating with other ports (8003, 8004, etc.).

---

## 45.3. Nginx Configuration

We want **one Nginx server** to serve both:

1. REST API (`/api/`) via Gunicorn
2. WebSockets (`/ws/`) via Daphne

Create a server block (e.g., `/etc/nginx/sites-available/jeco_chat`):

```nginx
upstream daphne_up {
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
}

server {
    listen 443 ssl;
    server_name app.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    # WebSocket proxy
    location /ws/ {
        proxy_pass http://daphne_up;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_read_timeout 86400;
    }

    # API proxy to Gunicorn
    location /api/ {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Serve static files (if collected)
    location /static/ {
        alias /var/www/jeco_chat/backend/static/;
    }

    location /media/ {
        alias /var/www/jeco_chat/backend/media/;
    }
}
```

### Notes

* `proxy_set_header Upgrade` + `Connection "Upgrade"` is **required** for WebSocket upgrade
* `proxy_read_timeout` ensures long-lived WS connections don’t timeout
* `upstream daphne_up` balances WS connections across multiple Daphne instances
* Static files served directly by Nginx; API requests go to Gunicorn (HTTP), WS requests go to Daphne (ASGI)

---

## 45.4. Reload Nginx

```bash
sudo nginx -t  # test syntax
sudo systemctl reload nginx
```

Check logs if connections fail:

```bash
sudo tail -f /var/log/nginx/error.log
```

---

## 45.5. Redis for Channel Layer

* Ensure **Redis is running** on same server or separate cluster:

```bash
sudo apt install redis-server
redis-cli ping  # should return PONG
```

* Use the same `REDIS_URL` in `settings.py`:

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {"hosts": [("127.0.0.1", 6379)]},
    },
}
```

---

## 45.6. Summary Checklist

1. Install Redis.
2. Configure `CHANNEL_LAYERS` in Django settings.
3. Configure `ChatConsumer` with JWT, presence, and rate limiting (from Step 44.a).
4. Create systemd template for Daphne and start 2+ instances.
5. Configure Nginx to proxy `/ws/` to Daphne upstream and `/api/` to Gunicorn.
6. Ensure SSL is enabled (HTTPS/WSS).
7. Reload Nginx.
8. Test WebSocket endpoints with token authentication (`wss://app.yourdomain.com/ws/chat/<room_name>/?token=<JWT>`).
9. Monitor `redis-cli`, `systemctl status jeco-daphne@*`, and Nginx logs.

✅ After completing these steps, Jeco Chat will be:

* **Scalable**: multiple Daphne instances handle WS concurrency
* **Secure**: JWT auth, origin check, WSS, rate limiting
* **Resilient**: Redis channel layer shares state across workers

---

If you want, I can now prepare a **full Docker Compose setup** including:

* Django backend
* Gunicorn
* Daphne (multiple instances)
* Redis channel layer
* Nginx reverse proxy
* PostgreSQL

…so you can deploy Jeco Chat in one command.

Do you want me to do that next?

Perfect! Let’s create a **full Docker Compose setup** for Jeco Chat. This will let you spin up the **entire stack** in one command: backend (Django + Daphne + Gunicorn), Redis, PostgreSQL, Nginx, and optional frontend.

---

# ⚡ Step 46: Docker Compose Setup for Jeco Chat

---

## 46.1 Directory Structure (recommended)

```
jeco_chat/
├─ backend/
│   ├─ manage.py
│   ├─ backend/       # Django project
│   ├─ chat/          # Django app
│   ├─ Dockerfile
│   ├─ requirements.txt
├─ frontend/
│   ├─ package.json
│   ├─ src/
│   ├─ Dockerfile
├─ nginx/
│   ├─ default.conf
├─ docker-compose.yml
```

---

## 46.2 Backend Dockerfile (`backend/Dockerfile`)

```dockerfile
FROM python:3.11-slim

# Install OS dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpq-dev \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Create working directory
WORKDIR /app

# Copy Python dependencies
COPY requirements.txt .
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Copy backend code
COPY . .

# Collect static files
RUN python manage.py collectstatic --noinput

# Expose Daphne ports for ASGI and Gunicorn for API
EXPOSE 8000  # Gunicorn API
EXPOSE 8001  # Daphne instance 1
EXPOSE 8002  # Daphne instance 2

# Entrypoint (adjust as needed)
CMD ["sh", "-c", "gunicorn backend.wsgi:application --bind 0.0.0.0:8000 --workers 3"]
```

---

## 46.3 Frontend Dockerfile (`frontend/Dockerfile`)

```dockerfile
FROM node:20-alpine

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .

# Build React app
RUN npm run build

# Expose port
EXPOSE 3000
CMD ["npm", "start"]
```

---

## 46.4 Nginx Config (`nginx/default.conf`)

```nginx
upstream daphne_up {
    server backend:8001;
    server backend:8002;
}

server {
    listen 80;

    server_name localhost;

    # Redirect all HTTP to HTTPS if desired
    # return 301 https://$host$request_uri;

    # WebSocket proxy
    location /ws/ {
        proxy_pass http://daphne_up;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_read_timeout 86400;
    }

    # API proxy to Gunicorn
    location /api/ {
        proxy_pass http://backend:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Serve static files
    location /static/ {
        alias /app/static/;
    }

    location /media/ {
        alias /app/media/;
    }

    # Frontend React build
    location / {
        root /frontend/build;
        index index.html;
        try_files $uri /index.html;
    }
}
```

---

## 46.5 Docker Compose File (`docker-compose.yml`)

```yaml
version: "3.9"

services:
  postgres:
    image: postgres:15
    container_name: jeco_postgres
    environment:
      POSTGRES_DB: jeco_db
      POSTGRES_USER: jeco_user
      POSTGRES_PASSWORD: jeco_pass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7
    container_name: jeco_redis
    ports:
      - "6379:6379"

  backend:
    build: ./backend
    container_name: jeco_backend
    command: >
      sh -c "
      python manage.py migrate &&
      python manage.py runserver 0.0.0.0:8000
      "
    volumes:
      - ./backend:/app
    environment:
      DJANGO_SETTINGS_MODULE: backend.settings
      POSTGRES_DB: jeco_db
      POSTGRES_USER: jeco_user
      POSTGRES_PASSWORD: jeco_pass
      DATABASE_URL: postgres://jeco_user:jeco_pass@postgres:5432/jeco_db
      REDIS_URL: redis://redis:6379/0
    depends_on:
      - postgres
      - redis
    ports:
      - "8000:8000"  # Gunicorn
      - "8001:8001"  # Daphne 1
      - "8002:8002"  # Daphne 2

  frontend:
    build: ./frontend
    container_name: jeco_frontend
    volumes:
      - ./frontend:/app
    ports:
      - "3000:3000"
    environment:
      REACT_APP_API_URL: http://localhost/api

  nginx:
    image: nginx:alpine
    container_name: jeco_nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./frontend/build:/frontend/build
      - ./backend/static:/app/static
      - ./backend/media:/app/media
    depends_on:
      - backend
      - frontend

volumes:
  postgres_data:
```

---

## 46.6 How to Run Everything

```bash
# Build images
docker-compose build

# Start all services
docker-compose up -d

# View logs
docker-compose logs -f
```

✅ This command will launch:

* PostgreSQL database
* Redis channel layer
* Django backend (Gunicorn + Daphne)
* React frontend
* Nginx reverse proxy for `/api/`, `/ws/`, static/media, and React frontend

---

## 46.7 Notes

* Adjust `.env` or `environment` variables for production (SECRET\_KEY, DEBUG=False, allowed hosts, JWT secrets).
* Daphne ports `8001`/`8002` are hardcoded in Nginx upstream. You can scale horizontally by adding more ports and adjusting `upstream`.
* Use Docker volumes for persistent data (`postgres_data`) and static/media if you don’t want to rebuild images every time.
* For production TLS, replace Nginx with certbot or use a reverse proxy like Traefik.

---

If you want, I can now create a **complete production checklist** including:

1. HTTPS/WSS setup
2. JWT refresh & access token rotation
3. Redis persistence and monitoring
4. Running Celery for notifications and background tasks

…so that the entire Jeco Chat stack is **production-ready**.

Do you want me to do that next?

Perfect! Let’s create a **complete production readiness checklist** for Jeco Chat, covering security, scalability, and background processing. This ensures your app is fully production-ready.

---

# ⚡ Step 47: Production Readiness Checklist for Jeco Chat

---

## 47.1 HTTPS / WSS Setup

1. **Obtain SSL certificates**

   * Use Let’s Encrypt (free) or commercial SSL.
   * Example with certbot:

     ```bash
     sudo apt install certbot python3-certbot-nginx
     sudo certbot --nginx -d app.yourdomain.com
     ```

2. **Enforce HTTPS / WSS**

   * Redirect all HTTP traffic to HTTPS in Nginx.
   * WebSocket connections must use WSS (`wss://app.yourdomain.com/ws/...`).

3. **Set HSTS headers** (optional but recommended) in Nginx:

   ```nginx
   add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
   ```

---

## 47.2 JWT Refresh & Access Token Rotation

1. **Use short-lived access tokens** (e.g., 5–15 min).
2. **Use long-lived refresh tokens** (e.g., 7 days) stored securely (HTTP-only, Secure cookies).
3. **Rotate refresh tokens** on every use to prevent replay attacks.
4. **Invalidate refresh tokens** on logout or password change.
5. **Django implementation**:

   * Use `rest_framework_simplejwt` with:

     ```python
     SIMPLE_JWT = {
         "ACCESS_TOKEN_LIFETIME": timedelta(minutes=15),
         "REFRESH_TOKEN_LIFETIME": timedelta(days=7),
         "ROTATE_REFRESH_TOKENS": True,
         "BLACKLIST_AFTER_ROTATION": True,
     }
     ```

---

## 47.3 CORS Hardening

1. Install `django-cors-headers`:

   ```bash
   pip install django-cors-headers
   ```

2. Add to `INSTALLED_APPS` and middleware in `settings.py`:

   ```python
   INSTALLED_APPS += ["corsheaders"]
   MIDDLEWARE = ["corsheaders.middleware.CorsMiddleware"] + MIDDLEWARE
   ```

3. Restrict allowed origins:

   ```python
   CORS_ALLOWED_ORIGINS = [
       "https://app.yourdomain.com",
   ]
   CORS_ALLOW_CREDENTIALS = True
   ```

4. Do **not** use `CORS_ALLOW_ALL_ORIGINS=True` in production.

---

## 47.4 Redis Persistence & Monitoring

1. **Persistence**

   * Enable AOF or RDB persistence in `redis.conf`:

     ```conf
     appendonly yes
     save 900 1
     save 300 10
     save 60 10000
     ```

2. **Monitoring**

   * Use `redis-cli info` for metrics.
   * Optional: integrate **Redis Exporter + Prometheus + Grafana**.

---

## 47.5 Celery for Background Tasks

Use Celery for:

* Sending emails (verification, notifications)
* Processing uploaded media (resizing images, generating thumbnails)
* Notifications for chat messages

**Celery Docker service example in `docker-compose.yml`:**

```yaml
celery:
  build: ./backend
  container_name: jeco_celery
  command: celery -A backend worker -l info
  depends_on:
    - redis
    - backend
  environment:
    - DATABASE_URL=postgres://jeco_user:jeco_pass@postgres:5432/jeco_db
    - REDIS_URL=redis://redis:6379/0
```

**Celery beat** (for scheduled tasks):

```yaml
celery-beat:
  build: ./backend
  container_name: jeco_celery_beat
  command: celery -A backend beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler
  depends_on:
    - redis
    - backend
```

---

## 47.6 Rate Limiting & Security

1. **Django REST API**:

   * Use `rest_framework.throttling` to limit requests per IP/user.

   ```python
   REST_FRAMEWORK = {
       'DEFAULT_THROTTLE_CLASSES': [
           'rest_framework.throttling.UserRateThrottle',
           'rest_framework.throttling.AnonRateThrottle',
       ],
       'DEFAULT_THROTTLE_RATES': {
           'user': '500/min',
           'anon': '50/min',
       }
   }
   ```

2. **WebSocket Rate Limiting**: Already implemented in ChatConsumer (Step 44.a).

3. **Enforce HTTPS-only cookies**:

   ```python
   SESSION_COOKIE_SECURE = True
   CSRF_COOKIE_SECURE = True
   ```

---

## 47.7 Logging & Monitoring

1. **Django Logging**:

   ```python
   LOGGING = {
       'version': 1,
       'disable_existing_loggers': False,
       'handlers': {
           'file': {
               'level': 'INFO',
               'class': 'logging.FileHandler',
               'filename': '/var/log/jeco_chat/app.log',
           },
       },
       'loggers': {
           'django': {
               'handlers': ['file'],
               'level': 'INFO',
               'propagate': True,
           },
           'chat.consumers': {
               'handlers': ['file'],
               'level': 'INFO',
               'propagate': True,
           },
       },
   }
   ```

2. **Monitor Docker containers**:

   ```bash
   docker stats
   docker-compose logs -f
   ```

3. Optional: integrate **Prometheus + Grafana** for metrics (WS connections, message counts, CPU/memory).

---

## 47.8 Database & Migrations

1. Run Django migrations on startup (already in Docker Compose backend command).
2. **Use PostgreSQL in production** with SSL enabled.
3. Optional: create read replicas if user base grows.

---

## 47.9 Static & Media Files

1. **Collect static files**: `python manage.py collectstatic --noinput`
2. **Store media in cloud storage** (S3, GCP, Azure) instead of `/media/` volume.

   * Example: `django-storages` with S3 backend.

---

## 47.10 Optional Scaling Tips

1. **Horizontal scaling**:

   * More Daphne instances behind Nginx upstream for WS.
   * Multiple Gunicorn workers for REST API.

2. **Celery workers** can be scaled horizontally for heavy tasks.

3. **Redis** can be clustered for high availability.

---

✅ Completing all steps in **Step 44–47** makes Jeco Chat:

* Fully **secure** (HTTPS/WSS, JWT, CORS)
* **Scalable** (multiple Daphne instances, Celery for async tasks)
* **Production-ready** (PostgreSQL + Redis + Nginx + static/media management)
* Monitored & maintainable

---

If you want, I can now produce a **full, final README.md walkthrough** that incorporates all **frontend, backend, WebSocket, Docker, and production steps** in a **single zero-knowledge, step-by-step checklist**.

Do you want me to do that next?
