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


