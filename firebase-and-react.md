
# **Firebase + React (Vite) – Complete Beginner Guide**

This guide shows you how to connect **React** to **Firebase** for:

* Authentication (login/signup)
* Firestore Database
* File Storage
* Hosting your React app


# **PART 1 — Create a Firebase Project**

### 1. Open Firebase

1. Go to **[https://firebase.google.com](https://firebase.google.com)**
2. Click **Get Started**
3. Sign in using your **Google account**

### 2. Create a New Firebase Project

1. Click **Add project**
2. Enter a **project name**
3. Click **Continue**
4. Enable **Google Analytics** (leave it on)
5. Click **Create project**
6. Click **Continue** → Firebase Console opens


### 3. Register Your Web App

Inside Firebase Console:

1. Click the **Web icon** `</>`
2. Enter an **app nickname**
3. Tick **"Also set up Firebase Hosting"** (optional)
4. Click **Register app**
5. Choose **"Use npm"**
6. You will see a **config object** like this:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

👉 **Do NOT close this page — we will use this later**

# **PART 2 — Create React App (Using Vite)**

### 1. Open Terminal

Run:

```bash
npm create vite@latest
```

Choose:

* Project name → `my-firebase-app`
* Framework → `React`
* Variant → `JavaScript`

Then run:

```bash
cd my-firebase-app
npm install
npm run dev
```

Your React app is now running.

# **PART 3 — Install Firebase in React**

Inside your React project:

```bash
npm install firebase
```

# **PART 4 — Create Firebase Config File**

Inside `src/`, create a new file:

```
src/firebase.js
```

Paste this inside:

```js
import { initializeApp } from "firebase/app";

const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_BUCKET",
  messagingSenderId: "YOUR_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);

export default app;
```

Replace values with the ones Firebase gave you.

# **PART 5 — Enable Authentication**

### Firebase Dashboard

1. Click **Authentication**
2. Click **Get Started**
3. Enable:

   * Email/Password
   * Google (optional)
4. Click **Save**

### React Setup (firebase.js)

Update `firebase.js`:

```js
import { getAuth } from "firebase/auth";
import app from "./firebase";

export const auth = getAuth(app);
```

Now your React app can use Firebase authentication.

# **PART 6 — Enable Firestore Database**

### Firebase

1. Click **Firestore Database**
2. Click **Create database**
3. Choose **Production Mode**
4. Select your region
5. Click **Enable**

### Add Firestore to React

In `firebase.js`:

```js
import { getFirestore } from "firebase/firestore";

export const db = getFirestore(app);
```

# **PART 7 — Enable Storage (For Images, Files, etc.)**

### Firebase

1. Click **Storage**
2. Click **Get Started**
3. Choose **Production Mode**
4. Click **Enable**

### Connect Storage to React

In `firebase.js`:

```js
import { getStorage } from "firebase/storage";

export const storage = getStorage(app);
```

# **PART 8 — Firebase CLI (For Hosting)**

Install Firebase tools:

```bash
npm install -g firebase-tools
```

Login:

```bash
firebase login
```

---

# **PART 9 — Build React App**

```bash
npm run build
```

Vite will create a folder called:

```
dist
```

(This is what Firebase hosts)

# **PART 10 — Connect Firebase Hosting**

Inside your React project:

```bash
firebase init
```

Choose:

* Hosting → Press space → Enter
* Use existing project → Select your Firebase project
* Public directory → `dist`
* Single Page App → Yes
* GitHub → Yes (optional)
* Overwrite files → No

# **PART 11 — Deploy Your App**

```bash
firebase deploy
```

Firebase will give you a **live website link** 🎉

# **What You Now Have**

Your React app now has:

* Login & Signup
* Database (Firestore)
* File Uploads (Storage)
* Live Website (Hosting)

