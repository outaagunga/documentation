# documentation
This is a repository to document all guide and walkthrough steps to create or work on different projects
Got it ✅
You’d like the README to be written like a **step-by-step to-do list / walkthrough**, so that someone (even with no coding background) can follow along: *“Do this… then do this… until the app is running.”*

Here’s a rewritten **README.md walkthrough** for **Jeco Chat** in that exact style:

---

# Jeco Chat — Walkthrough To-Do List

This is your **step-by-step to-do list** to build and run **Jeco Chat**, a real-time social networking app where users can chat, share, and connect.

Even if you’ve never coded before, just follow each step in order.

---

## 📝 Step 1 — Install Prerequisites

Before doing anything, install these on your computer:

1. **Git** → [Download here](https://git-scm.com/downloads)
2. **Python 3.11+** → [Download here](https://www.python.org/downloads/)

   * Check:

     ```bash
     python --version
     ```
3. **Node.js (18+) and npm** → [Download here](https://nodejs.org/)

   * Check:

     ```bash
     node -v
     npm -v
     ```
4. **PostgreSQL 14+** → [Download here](https://www.postgresql.org/download/)

   * Remember your **username**, **password**, and **port**.
5. **Redis** (for real-time chat):

   * macOS:

     ```bash
     brew install redis && brew services start redis
     ```
   * Linux:

     ```bash
     sudo apt install redis-server && sudo systemctl enable --now redis
     ```
   * Windows: use **WSL** or Docker:

     ```bash
     docker run -p 6379:6379 redis
     ```

✅ Once installed, move to the next step.

---

## 📝 Step 2 — Get the Project Code

1. Open your terminal (Command Prompt, PowerShell, Git Bash, or macOS/Linux terminal).
2. Clone the repository:

```bash
git clone https://github.com/your-username/jeco-chat.git
cd jeco-chat
```

> If you don’t have a repository yet, just create a new folder called `jeco-chat` and follow along.

---

## 📝 Step 3 — Backend Setup (Django + DRF + Channels)

### 3.1 Create a virtual environment

```bash
cd backend
python -m venv .venv           # Windows: py -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install --upgrade pip
```

### 3.2 Install dependencies

Create a `requirements.txt` with this content:

```txt
Django
djangorestframework
djangorestframework-simplejwt
django-cors-headers
psycopg[binary,pool]
channels
channels-redis
Pillow
python-dotenv
```

Then run:

```bash
pip install -r requirements.txt
```

### 3.3 Create a `.env` file

Inside `backend/`, create `.env`:

```env
DJANGO_SECRET_KEY=change-me-please
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

POSTGRES_DB=jecochat
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_db_password
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

CORS_ALLOWED_ORIGINS=http://localhost:5173
CSRF_TRUSTED_ORIGINS=http://localhost:5173

REDIS_URL=redis://127.0.0.1:6379/0
```

### 3.4 Run migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

### 3.5 Create a superuser

```bash
python manage.py createsuperuser
```

Follow the prompts to set username/password.

### 3.6 Start backend server

```bash
python manage.py runserver
```

✅ Your backend is now running at `http://127.0.0.1:8000/`

---

## 📝 Step 4 — Frontend Setup (React + Bootstrap)

### 4.1 Create frontend

From the root folder `jeco-chat/`:

```bash
mkdir frontend && cd frontend
npm create vite@latest . -- --template react
npm install
```

### 4.2 Install dependencies

```bash
npm install bootstrap react-router-dom axios
```

### 4.3 Configure Bootstrap

Edit `src/main.jsx` and add:

```jsx
import 'bootstrap/dist/css/bootstrap.min.css';
```

### 4.4 Add API base URL

Create `.env` in `frontend/`:

```env
VITE_API_BASE_URL=http://127.0.0.1:8000/api
VITE_WS_BASE_URL=ws://127.0.0.1:8000/ws
```

### 4.5 Start frontend server

```bash
npm run dev
```

✅ Your frontend is now running at `http://localhost:5173/`

---

## 📝 Step 5 — Run Both Together

1. Open **two terminals**.

   * Terminal A:

     ```bash
     cd backend
     source .venv/bin/activate   # Windows: .venv\Scripts\activate
     python manage.py runserver
     ```
   * Terminal B:

     ```bash
     cd frontend
     npm run dev
     ```
2. Visit: **[http://localhost:5173](http://localhost:5173)**

---

## 📝 Step 6 — Test the API

Try registering a new user:

```bash
curl -X POST http://127.0.0.1:8000/api/auth/register/ \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Pass1234","username":"tester"}'
```

If you see a response with a user ID, your backend works.

---

## 📝 Step 7 — Chat in Real-Time

1. Login on the frontend.
2. Open **two browser tabs** with two different accounts.
3. Join the same chat room.
4. Type a message — it should instantly appear in both tabs (via WebSockets).

✅ Congratulations! You’ve built Jeco Chat 🎉

---

## 🚀 Next Steps

* Deploy backend on **Render**, **Railway**, or **DigitalOcean**.
* Deploy frontend on **Vercel** or **Netlify**.
* Use Docker Compose for PostgreSQL + Redis + Django + React.

---

## ⚡ Troubleshooting Quick Fixes

* **Database error** → check your `.env` PostgreSQL username/password.
* **Redis error** → start Redis with `redis-server` or Docker.
* **CORS error** → make sure `.env` frontend URL matches your actual frontend port.
* **Frontend not loading API** → check `VITE_API_BASE_URL` is correct.

---

## 📄 License

MIT License © \[2025] \[Your Name]

---

Would you like me to **add checkboxes `[ ]` in front of each step**, so the README becomes a real interactive to-do checklist developers can tick off as they go?
