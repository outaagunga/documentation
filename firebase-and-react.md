
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

Create a project folder

### 1. Open Terminal

`cd` to the project folder
Run:

```bash
npm create vite@latest .
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
- Then select collection (collection is like a table in the database)
- Select auto id, then add different fields to the database
- Then set rules on the database
- Then create a function to expose the database e.g `Export const db=getFirestore(app);`
- Then go to App.js and import db also  

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
yarn build
```

Vite will create a folder called:

```
build
```

(This is what Firebase hosts)

# **PART 10 — Connect Firebase Hosting**

Inside your React project:

```bash
firebase init
```

Choose:

* Hosting configure files for firebase hosting and optionally set up Github action deploys → Press space → Enter
* Use existing project → Select your Firebase project
* Public directory → `build`
* Single Page App → No
* GitHub → Yes (optional)
* `Public/index.html` exists. Overwrite files → No
* Then set up firebase Github login on the pop up window
* Then add the github repository you want to use for the project, i.e copy the github repository url. Copy the part that start from the `username` to the end and paste it on the terminal, then press enter
* Select yes, yes again, then select branch e.g main.

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



Role: Act as a Senior Full-Stack Developer.
Task: Provide a complete boilerplate for a Task Manager App using React (Vite) and Firebase v10+.
Technical Requirements:
1.	Authentication: Implement Firebase Auth using Email/Password and Google Provider.
2.	Database: Use Firestore. Ensure strict User Data Isolation (users can only CRUD their own documents).
3.	State Management & "Real-time" UI: Do not use onSnapshot for real-time updates. Instead, implement a Centralized State (React Context API) to manage tasks. Use Optimistic UI updates—when a user adds or deletes a task, update the React state immediately before the Firestore promise resolves to ensure the app feels "real-time."
4.	Architecture: Use a modular component-based structure. Separate Firebase configuration, API services, and UI components.
Output Format:
•	Project Directory Structure: A tree view of the folders and files.
•	Firebase Security Rules: Provide the Firestore rules for user isolation.
•	Code Files: Provide the code for firebase.js (config), AuthContext.js, TaskContext.js, and the primary UI components (Login, Dashboard, TaskItem).
•	Do it in segments if your memory/ limits does not allow you to do it all at once.

I'll provide you with a complete, production-ready boilerplate for a Task Manager App with Firebase v10+. Let me break this down into organized segments.

## **Segment 1: Project Structure & Firebase Configuration**

### **Project Directory Structure**

```
task-manager-app/
├── public/
├── src/
│   ├── components/
│   │   ├── auth/
│   │   │   ├── Login.jsx
│   │   │   └── PrivateRoute.jsx
│   │   ├── tasks/
│   │   │   ├── TaskItem.jsx
│   │   │   ├── TaskList.jsx
│   │   │   └── TaskForm.jsx
│   │   └── layout/
│   │       ├── Navbar.jsx
│   │       └── Dashboard.jsx
│   ├── contexts/
│   │   ├── AuthContext.jsx
│   │   └── TaskContext.jsx
│   ├── services/
│   │   ├── firebase.js
│   │   └── taskService.js
│   ├── hooks/
│   │   └── useAuth.js
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── .env
├── .gitignore
├── package.json
└── vite.config.js
```

### **Environment Variables (.env)**

```env
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your_project_id
VITE_FIREBASE_STORAGE_BUCKET=your_project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
VITE_FIREBASE_APP_ID=your_app_id
```

I'll provide you with a complete, production-ready boilerplate for a Task Manager App with Firebase v10+. Let me break this down into organized segments.

## **Segment 1: Project Structure & Firebase Configuration**

### **Project Directory Structure**

```
task-manager-app/
├── public/
├── src/
│   ├── components/
│   │   ├── auth/
│   │   │   ├── Login.jsx
│   │   │   └── PrivateRoute.jsx
│   │   ├── tasks/
│   │   │   ├── TaskItem.jsx
│   │   │   ├── TaskList.jsx
│   │   │   └── TaskForm.jsx
│   │   └── layout/
│   │       ├── Navbar.jsx
│   │       └── Dashboard.jsx
│   ├── contexts/
│   │   ├── AuthContext.jsx
│   │   └── TaskContext.jsx
│   ├── services/
│   │   ├── firebase.js
│   │   └── taskService.js
│   ├── hooks/
│   │   └── useAuth.js
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── .env
├── .gitignore
├── package.json
└── vite.config.js
```

### **Environment Variables (.env)**

```env
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your_project_id
VITE_FIREBASE_STORAGE_BUCKET=your_project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
VITE_FIREBASE_APP_ID=your_app_id
```

### **Firebase Configuration (src/services/firebase.js)**


### **Firestore Security Rules**---

## **Segment 2: Authentication Context & Service**

### **Auth Context (src/contexts/AuthContext.jsx)**


### **Custom Hook (src/hooks/useAuth.js)**---

## **Segment 3: Task Context & Service (Optimistic UI)**

### **Task Service (src/services/taskService.js)**


### **Task Context with Optimistic UI (src/contexts/TaskContext.jsx)**---


## **Segment 4: Authentication Components**


### **Login Component (src/components/auth/Login.jsx)**



### **Private Route Component (src/components/auth/PrivateRoute.jsx)**



I'll continue with the remaining segments in my next response. So far we've covered:

## **Segment 5: Task Components**

### **Task Form Component (src/components/tasks/TaskForm.jsx)**


### **Task Item Component (src/components/tasks/TaskItem.jsx)**



### **Task List Component (src/components/tasks/TaskList.jsx)**---




## **Segment 6: Layout Components**



### **Navbar Component (src/components/layout/Navbar.jsx)**



### **Dashboard Component (src/components/layout/Dashboard.jsx)**---


## **Segment 7: Main App Files**


### **App.jsx (src/App.jsx)**



### **main.jsx (src/main.jsx)**


### **index.css (src/index.css)**---

## **Segment 8: Configuration Files**


### **package.json**


### **vite.config.js**


### **tailwind.config.js**


### **postcss.config.js**


### **.gitignore**



### **index.html (public/index.html or root)**---


## **Segment 9: Setup Instructions & Documentation**---


## **Complete! 🎉**

### 📁 All Artifacts Created:

1. firebase.js - Firebase configuration
2. firestore.rules - Security rules
3. AuthContext.jsx - Authentication state
4. TaskContext.jsx - Task state with optimistic UI
5. taskService.js - Firestore operations
6. Login.jsx - Auth UI
7. PrivateRoute.jsx - Route protection
8. TaskForm.jsx - Add tasks
9. TaskItem.jsx - Individual task
10. TaskList.jsx - Task list with filters
11. Navbar.jsx - Navigation
12. Dashboard.jsx - Main layout
13. App.jsx - Routes
14. Configuration files (package.json, vite.config.js, etc.)
15. README.md - Complete setup guide
