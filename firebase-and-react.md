
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
if you get error use this command
```bash
curl -sL https://firebase.tools | bash
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
dist
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
```js
// src/services/firebase.js
import { initializeApp } from 'firebase/app';
import { getAuth, GoogleAuthProvider } from 'firebase/auth';
import { getFirestore } from 'firebase/firestore';

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

// Initialize Firebase services
export const auth = getAuth(app);
export const db = getFirestore(app);
export const googleProvider = new GoogleAuthProvider();

// Optional: Configure Google Provider
googleProvider.setCustomParameters({
  prompt: 'select_account'
});

export default app;
```


### **Firestore Security Rules**---
```js
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper function to check if user is authenticated
    function isAuthenticated() {
      return request.auth != null;
    }
    
    // Helper function to check if user owns the document
    function isOwner(userId) {
      return isAuthenticated() && request.auth.uid == userId;
    }
    
    // Tasks collection - strict user isolation
    match /tasks/{taskId} {
      // Users can only read their own tasks
      allow read: if isAuthenticated() && 
                     resource.data.userId == request.auth.uid;
      
      // Users can only create tasks with their own userId
      allow create: if isAuthenticated() && 
                       request.resource.data.userId == request.auth.uid &&
                       request.resource.data.keys().hasAll(['title', 'userId', 'createdAt']) &&
                       request.resource.data.userId is string &&
                       request.resource.data.title is string;
      
      // Users can only update their own tasks
      allow update: if isAuthenticated() && 
                       resource.data.userId == request.auth.uid &&
                       request.resource.data.userId == request.auth.uid;
      
      // Users can only delete their own tasks
      allow delete: if isAuthenticated() && 
                       resource.data.userId == request.auth.uid;
    }
    
    // Deny all other access by default
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

## **Segment 2: Authentication Context & Service**

### **Auth Context (src/contexts/AuthContext.jsx)**
```js
// src/contexts/AuthContext.jsx
import { createContext, useContext, useEffect, useState } from 'react';
import {
  createUserWithEmailAndPassword,
  signInWithEmailAndPassword,
  signInWithPopup,
  signOut,
  onAuthStateChanged,
  updateProfile
} from 'firebase/auth';
import { auth, googleProvider } from '../services/firebase';

const AuthContext = createContext({});

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};

export const AuthProvider = ({ children }) => {
  const [currentUser, setCurrentUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Sign up with email and password
  const signup = async (email, password, displayName) => {
    try {
      setError(null);
      const userCredential = await createUserWithEmailAndPassword(auth, email, password);
      
      // Update profile with display name
      if (displayName) {
        await updateProfile(userCredential.user, { displayName });
      }
      
      return userCredential;
    } catch (err) {
      setError(err.message);
      throw err;
    }
  };

  // Sign in with email and password
  const login = async (email, password) => {
    try {
      setError(null);
      return await signInWithEmailAndPassword(auth, email, password);
    } catch (err) {
      setError(err.message);
      throw err;
    }
  };

  // Sign in with Google
  const loginWithGoogle = async () => {
    try {
      setError(null);
      return await signInWithPopup(auth, googleProvider);
    } catch (err) {
      setError(err.message);
      throw err;
    }
  };

  // Sign out
  const logout = async () => {
    try {
      setError(null);
      return await signOut(auth);
    } catch (err) {
      setError(err.message);
      throw err;
    }
  };

  // Listen to auth state changes
  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (user) => {
      setCurrentUser(user);
      setLoading(false);
    });

    return unsubscribe;
  }, []);

  const value = {
    currentUser,
    loading,
    error,
    signup,
    login,
    loginWithGoogle,
    logout
  };

  return (
    <AuthContext.Provider value={value}>
      {!loading && children}
    </AuthContext.Provider>
  );
};
```

### **Custom Hook (src/hooks/useAuth.js)**---
```js
// src/hooks/useAuth.js
import { useContext } from 'react';
import { AuthContext } from '../contexts/AuthContext';

// This is exported from AuthContext.jsx but can be imported separately
export { useAuth } from '../contexts/AuthContext';
```

## **Segment 3: Task Context & Service (Optimistic UI)**

### **Task Service (src/services/taskService.js)**
```js
// src/services/taskService.js
import {
  collection,
  addDoc,
  updateDoc,
  deleteDoc,
  doc,
  query,
  where,
  getDocs,
  orderBy,
  serverTimestamp
} from 'firebase/firestore';
import { db } from './firebase';

const TASKS_COLLECTION = 'tasks';

export const taskService = {
  // Fetch all tasks for a specific user
  async getTasks(userId) {
    try {
      const tasksRef = collection(db, TASKS_COLLECTION);
      const q = query(
        tasksRef,
        where('userId', '==', userId),
        orderBy('createdAt', 'desc')
      );
      
      const querySnapshot = await getDocs(q);
      const tasks = [];
      
      querySnapshot.forEach((doc) => {
        tasks.push({
          id: doc.id,
          ...doc.data()
        });
      });
      
      return tasks;
    } catch (error) {
      console.error('Error fetching tasks:', error);
      throw error;
    }
  },

  // Add a new task
  async addTask(taskData) {
    try {
      const tasksRef = collection(db, TASKS_COLLECTION);
      const docRef = await addDoc(tasksRef, {
        ...taskData,
        createdAt: serverTimestamp(),
        updatedAt: serverTimestamp()
      });
      
      return {
        id: docRef.id,
        ...taskData,
        createdAt: new Date(),
        updatedAt: new Date()
      };
    } catch (error) {
      console.error('Error adding task:', error);
      throw error;
    }
  },

  // Update an existing task
  async updateTask(taskId, updates) {
    try {
      const taskRef = doc(db, TASKS_COLLECTION, taskId);
      await updateDoc(taskRef, {
        ...updates,
        updatedAt: serverTimestamp()
      });
      
      return {
        id: taskId,
        ...updates,
        updatedAt: new Date()
      };
    } catch (error) {
      console.error('Error updating task:', error);
      throw error;
    }
  },

  // Delete a task
  async deleteTask(taskId) {
    try {
      const taskRef = doc(db, TASKS_COLLECTION, taskId);
      await deleteDoc(taskRef);
      return taskId;
    } catch (error) {
      console.error('Error deleting task:', error);
      throw error;
    }
  },

  // Toggle task completion
  async toggleTaskComplete(taskId, currentStatus) {
    try {
      return await this.updateTask(taskId, {
        completed: !currentStatus
      });
    } catch (error) {
      console.error('Error toggling task:', error);
      throw error;
    }
  }
};
```

### **Task Context with Optimistic UI (src/contexts/TaskContext.jsx)**---
```js
// src/contexts/TaskContext.jsx
import { createContext, useContext, useState, useEffect, useCallback } from 'react';
import { useAuth } from './AuthContext';
import { taskService } from '../services/taskService';

const TaskContext = createContext({});

export const useTask = () => {
  const context = useContext(TaskContext);
  if (!context) {
    throw new Error('useTask must be used within a TaskProvider');
  }
  return context;
};

export const TaskProvider = ({ children }) => {
  const { currentUser } = useAuth();
  const [tasks, setTasks] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Fetch tasks when user logs in
  const fetchTasks = useCallback(async () => {
    if (!currentUser) {
      setTasks([]);
      setLoading(false);
      return;
    }

    try {
      setLoading(true);
      setError(null);
      const fetchedTasks = await taskService.getTasks(currentUser.uid);
      setTasks(fetchedTasks);
    } catch (err) {
      console.error('Error fetching tasks:', err);
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, [currentUser]);

  // Load tasks on mount and when user changes
  useEffect(() => {
    fetchTasks();
  }, [fetchTasks]);

  // Add task with optimistic UI
  const addTask = async (taskData) => {
    if (!currentUser) {
      throw new Error('User must be authenticated');
    }

    // Generate temporary ID for optimistic update
    const tempId = `temp-${Date.now()}`;
    const optimisticTask = {
      id: tempId,
      ...taskData,
      userId: currentUser.uid,
      completed: false,
      createdAt: new Date(),
      updatedAt: new Date()
    };

    // Optimistic update - add immediately to UI
    setTasks(prevTasks => [optimisticTask, ...prevTasks]);

    try {
      // Perform actual Firebase operation
      const newTask = await taskService.addTask({
        ...taskData,
        userId: currentUser.uid,
        completed: false
      });

      // Replace optimistic task with real task
      setTasks(prevTasks =>
        prevTasks.map(task => task.id === tempId ? newTask : task)
      );

      return newTask;
    } catch (err) {
      // Rollback on error - remove optimistic task
      setTasks(prevTasks => prevTasks.filter(task => task.id !== tempId));
      setError(err.message);
      throw err;
    }
  };

  // Update task with optimistic UI
  const updateTask = async (taskId, updates) => {
    // Store original task for rollback
    const originalTask = tasks.find(task => task.id === taskId);
    if (!originalTask) return;

    // Optimistic update
    setTasks(prevTasks =>
      prevTasks.map(task =>
        task.id === taskId
          ? { ...task, ...updates, updatedAt: new Date() }
          : task
      )
    );

    try {
      await taskService.updateTask(taskId, updates);
    } catch (err) {
      // Rollback on error
      setTasks(prevTasks =>
        prevTasks.map(task => task.id === taskId ? originalTask : task)
      );
      setError(err.message);
      throw err;
    }
  };

  // Delete task with optimistic UI
  const deleteTask = async (taskId) => {
    // Store original task for rollback
    const originalTask = tasks.find(task => task.id === taskId);
    if (!originalTask) return;

    // Optimistic update - remove immediately
    setTasks(prevTasks => prevTasks.filter(task => task.id !== taskId));

    try {
      await taskService.deleteTask(taskId);
    } catch (err) {
      // Rollback on error - restore task
      setTasks(prevTasks => [...prevTasks, originalTask]);
      setError(err.message);
      throw err;
    }
  };

  // Toggle task completion
  const toggleTaskComplete = async (taskId) => {
    const task = tasks.find(t => t.id === taskId);
    if (!task) return;

    await updateTask(taskId, { completed: !task.completed });
  };

  const value = {
    tasks,
    loading,
    error,
    addTask,
    updateTask,
    deleteTask,
    toggleTaskComplete,
    refreshTasks: fetchTasks
  };

  return (
    <TaskContext.Provider value={value}>
      {children}
    </TaskContext.Provider>
  );
};
```

## **Segment 4: Authentication Components**

### **Login Component (src/components/auth/Login.jsx)**
```js
// src/components/auth/Login.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuth } from '../../contexts/AuthContext';

export default function Login() {
  const [isLogin, setIsLogin] = useState(true);
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [displayName, setDisplayName] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const { login, signup, loginWithGoogle } = useAuth();
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');
    setLoading(true);

    try {
      if (isLogin) {
        await login(email, password);
      } else {
        await signup(email, password, displayName);
      }
      navigate('/dashboard');
    } catch (err) {
      setError(err.message || 'Failed to authenticate');
    } finally {
      setLoading(false);
    }
  };

  const handleGoogleLogin = async () => {
    setError('');
    setLoading(true);

    try {
      await loginWithGoogle();
      navigate('/dashboard');
    } catch (err) {
      setError(err.message || 'Failed to login with Google');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
      <div className="max-w-md w-full space-y-8">
        <div>
          <h2 className="mt-6 text-center text-3xl font-extrabold text-gray-900">
            {isLogin ? 'Sign in to your account' : 'Create new account'}
          </h2>
        </div>

        {error && (
          <div className="bg-red-50 border border-red-400 text-red-700 px-4 py-3 rounded relative">
            {error}
          </div>
        )}

        <form className="mt-8 space-y-6" onSubmit={handleSubmit}>
          <div className="rounded-md shadow-sm -space-y-px">
            {!isLogin && (
              <div>
                <label htmlFor="displayName" className="sr-only">
                  Display Name
                </label>
                <input
                  id="displayName"
                  name="displayName"
                  type="text"
                  required={!isLogin}
                  value={displayName}
                  onChange={(e) => setDisplayName(e.target.value)}
                  className="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                  placeholder="Display Name"
                />
              </div>
            )}
            
            <div>
              <label htmlFor="email-address" className="sr-only">
                Email address
              </label>
              <input
                id="email-address"
                name="email"
                type="email"
                autoComplete="email"
                required
                value={email}
                onChange={(e) => setEmail(e.target.value)}
                className={`appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 ${
                  isLogin ? 'rounded-t-md' : ''
                } focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm`}
                placeholder="Email address"
              />
            </div>
            
            <div>
              <label htmlFor="password" className="sr-only">
                Password
              </label>
              <input
                id="password"
                name="password"
                type="password"
                autoComplete="current-password"
                required
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                className="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                placeholder="Password"
              />
            </div>
          </div>

          <div>
            <button
              type="submit"
              disabled={loading}
              className="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:opacity-50"
            >
              {loading ? 'Loading...' : isLogin ? 'Sign in' : 'Sign up'}
            </button>
          </div>

          <div className="relative">
            <div className="absolute inset-0 flex items-center">
              <div className="w-full border-t border-gray-300"></div>
            </div>
            <div className="relative flex justify-center text-sm">
              <span className="px-2 bg-gray-50 text-gray-500">Or continue with</span>
            </div>
          </div>

          <div>
            <button
              type="button"
              onClick={handleGoogleLogin}
              disabled={loading}
              className="w-full flex justify-center items-center py-2 px-4 border border-gray-300 rounded-md shadow-sm bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:opacity-50"
            >
              <svg className="w-5 h-5 mr-2" viewBox="0 0 24 24">
                <path
                  fill="#4285F4"
                  d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"
                />
                <path
                  fill="#34A853"
                  d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"
                />
                <path
                  fill="#FBBC05"
                  d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"
                />
                <path
                  fill="#EA4335"
                  d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"
                />
              </svg>
              Google
            </button>
          </div>

          <div className="text-center">
            <button
              type="button"
              onClick={() => setIsLogin(!isLogin)}
              className="text-sm text-indigo-600 hover:text-indigo-500"
            >
              {isLogin
                ? "Don't have an account? Sign up"
                : 'Already have an account? Sign in'}
            </button>
          </div>
        </form>
      </div>
    </div>
  );
}
```

### **Private Route Component (src/components/auth/PrivateRoute.jsx)**
```js
// src/components/auth/PrivateRoute.jsx
import { Navigate } from 'react-router-dom';
import { useAuth } from '../../contexts/AuthContext';

export default function PrivateRoute({ children }) {
  const { currentUser, loading } = useAuth();

  if (loading) {
    return (
      <div className="min-h-screen flex items-center justify-center">
        <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-indigo-600"></div>
      </div>
    );
  }

  return currentUser ? children : <Navigate to="/login" />;
}
```

## **Segment 5: Task Components**

### **Task Form Component (src/components/tasks/TaskForm.jsx)**
```js
// src/components/tasks/TaskForm.jsx
import { useState } from 'react';
import { useTask } from '../../contexts/TaskContext';

export default function TaskForm() {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');
  const [priority, setPriority] = useState('medium');
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const { addTask } = useTask();

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    if (!title.trim()) {
      setError('Task title is required');
      return;
    }

    setLoading(true);
    setError('');

    try {
      await addTask({
        title: title.trim(),
        description: description.trim(),
        priority,
      });

      // Reset form
      setTitle('');
      setDescription('');
      setPriority('medium');
    } catch (err) {
      setError(err.message || 'Failed to add task');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="bg-white shadow-md rounded-lg p-6 mb-6">
      <h2 className="text-xl font-semibold text-gray-800 mb-4">Add New Task</h2>
      
      {error && (
        <div className="mb-4 bg-red-50 border border-red-400 text-red-700 px-4 py-3 rounded">
          {error}
        </div>
      )}

      <form onSubmit={handleSubmit} className="space-y-4">
        <div>
          <label htmlFor="title" className="block text-sm font-medium text-gray-700 mb-1">
            Task Title *
          </label>
          <input
            type="text"
            id="title"
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            placeholder="Enter task title"
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500"
            disabled={loading}
          />
        </div>

        <div>
          <label htmlFor="description" className="block text-sm font-medium text-gray-700 mb-1">
            Description
          </label>
          <textarea
            id="description"
            value={description}
            onChange={(e) => setDescription(e.target.value)}
            placeholder="Enter task description (optional)"
            rows="3"
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500"
            disabled={loading}
          />
        </div>

        <div>
          <label htmlFor="priority" className="block text-sm font-medium text-gray-700 mb-1">
            Priority
          </label>
          <select
            id="priority"
            value={priority}
            onChange={(e) => setPriority(e.target.value)}
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500"
            disabled={loading}
          >
            <option value="low">Low</option>
            <option value="medium">Medium</option>
            <option value="high">High</option>
          </select>
        </div>

        <button
          type="submit"
          disabled={loading}
          className="w-full bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
        >
          {loading ? 'Adding...' : 'Add Task'}
        </button>
      </form>
    </div>
  );
}
```

### **Task Item Component (src/components/tasks/TaskItem.jsx)**
```js
// src/components/tasks/TaskItem.jsx
import { useState } from 'react';
import { useTask } from '../../contexts/TaskContext';

export default function TaskItem({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const [editTitle, setEditTitle] = useState(task.title);
  const [editDescription, setEditDescription] = useState(task.description || '');
  const [editPriority, setEditPriority] = useState(task.priority || 'medium');

  const { updateTask, deleteTask, toggleTaskComplete } = useTask();

  const handleToggleComplete = async () => {
    try {
      await toggleTaskComplete(task.id);
    } catch (err) {
      console.error('Failed to toggle task:', err);
    }
  };

  const handleDelete = async () => {
    if (window.confirm('Are you sure you want to delete this task?')) {
      try {
        await deleteTask(task.id);
      } catch (err) {
        console.error('Failed to delete task:', err);
      }
    }
  };

  const handleUpdate = async (e) => {
    e.preventDefault();
    
    if (!editTitle.trim()) {
      alert('Task title cannot be empty');
      return;
    }

    try {
      await updateTask(task.id, {
        title: editTitle.trim(),
        description: editDescription.trim(),
        priority: editPriority,
      });
      setIsEditing(false);
    } catch (err) {
      console.error('Failed to update task:', err);
    }
  };

  const handleCancelEdit = () => {
    setEditTitle(task.title);
    setEditDescription(task.description || '');
    setEditPriority(task.priority || 'medium');
    setIsEditing(false);
  };

  const getPriorityColor = (priority) => {
    switch (priority) {
      case 'high':
        return 'bg-red-100 text-red-800 border-red-200';
      case 'medium':
        return 'bg-yellow-100 text-yellow-800 border-yellow-200';
      case 'low':
        return 'bg-green-100 text-green-800 border-green-200';
      default:
        return 'bg-gray-100 text-gray-800 border-gray-200';
    }
  };

  if (isEditing) {
    return (
      <div className="bg-white border border-gray-200 rounded-lg p-4 shadow-sm">
        <form onSubmit={handleUpdate} className="space-y-3">
          <input
            type="text"
            value={editTitle}
            onChange={(e) => setEditTitle(e.target.value)}
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            placeholder="Task title"
          />
          
          <textarea
            value={editDescription}
            onChange={(e) => setEditDescription(e.target.value)}
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            placeholder="Task description"
            rows="2"
          />

          <select
            value={editPriority}
            onChange={(e) => setEditPriority(e.target.value)}
            className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
          >
            <option value="low">Low Priority</option>
            <option value="medium">Medium Priority</option>
            <option value="high">High Priority</option>
          </select>

          <div className="flex space-x-2">
            <button
              type="submit"
              className="flex-1 bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500"
            >
              Save
            </button>
            <button
              type="button"
              onClick={handleCancelEdit}
              className="flex-1 bg-gray-300 text-gray-700 py-2 px-4 rounded-md hover:bg-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500"
            >
              Cancel
            </button>
          </div>
        </form>
      </div>
    );
  }

  return (
    <div className={`bg-white border border-gray-200 rounded-lg p-4 shadow-sm transition-all ${
      task.completed ? 'opacity-60' : ''
    }`}>
      <div className="flex items-start space-x-3">
        <input
          type="checkbox"
          checked={task.completed || false}
          onChange={handleToggleComplete}
          className="mt-1 h-5 w-5 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded cursor-pointer"
        />
        
        <div className="flex-1 min-w-0">
          <h3 className={`text-lg font-medium text-gray-900 ${
            task.completed ? 'line-through' : ''
          }`}>
            {task.title}
          </h3>
          
          {task.description && (
            <p className={`mt-1 text-sm text-gray-600 ${
              task.completed ? 'line-through' : ''
            }`}>
              {task.description}
            </p>
          )}
          
          <div className="mt-2 flex items-center space-x-2">
            <span className={`inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium border ${
              getPriorityColor(task.priority)
            }`}>
              {task.priority || 'medium'}
            </span>
            
            {task.createdAt && (
              <span className="text-xs text-gray-500">
                {new Date(task.createdAt.seconds * 1000 || task.createdAt).toLocaleDateString()}
              </span>
            )}
          </div>
        </div>

        <div className="flex space-x-2">
          <button
            onClick={() => setIsEditing(true)}
            className="text-indigo-600 hover:text-indigo-800 focus:outline-none"
            title="Edit task"
          >
            <svg className="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
            </svg>
          </button>
          
          <button
            onClick={handleDelete}
            className="text-red-600 hover:text-red-800 focus:outline-none"
            title="Delete task"
          >
            <svg className="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
            </svg>
          </button>
        </div>
      </div>
    </div>
  );
}
```


### **Task List Component (src/components/tasks/TaskList.jsx)**---
```js
// src/components/tasks/TaskList.jsx
import { useState, useMemo } from 'react';
import { useTask } from '../../contexts/TaskContext';
import TaskItem from './TaskItem';

export default function TaskList() {
  const { tasks, loading, error } = useTask();
  const [filter, setFilter] = useState('all'); // all, active, completed
  const [sortBy, setSortBy] = useState('date'); // date, priority, title

  const filteredAndSortedTasks = useMemo(() => {
    let filtered = tasks;

    // Apply filter
    if (filter === 'active') {
      filtered = tasks.filter(task => !task.completed);
    } else if (filter === 'completed') {
      filtered = tasks.filter(task => task.completed);
    }

    // Apply sort
    const sorted = [...filtered].sort((a, b) => {
      if (sortBy === 'priority') {
        const priorityOrder = { high: 3, medium: 2, low: 1 };
        return (priorityOrder[b.priority] || 2) - (priorityOrder[a.priority] || 2);
      } else if (sortBy === 'title') {
        return a.title.localeCompare(b.title);
      } else {
        // Sort by date (newest first)
        const aDate = a.createdAt?.seconds || a.createdAt?.getTime?.() / 1000 || 0;
        const bDate = b.createdAt?.seconds || b.createdAt?.getTime?.() / 1000 || 0;
        return bDate - aDate;
      }
    });

    return sorted;
  }, [tasks, filter, sortBy]);

  const stats = useMemo(() => {
    return {
      total: tasks.length,
      active: tasks.filter(t => !t.completed).length,
      completed: tasks.filter(t => t.completed).length,
    };
  }, [tasks]);

  if (loading) {
    return (
      <div className="flex justify-center items-center py-12">
        <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-indigo-600"></div>
      </div>
    );
  }

  if (error) {
    return (
      <div className="bg-red-50 border border-red-400 text-red-700 px-4 py-3 rounded">
        Error loading tasks: {error}
      </div>
    );
  }

  return (
    <div>
      {/* Stats */}
      <div className="grid grid-cols-3 gap-4 mb-6">
        <div className="bg-white p-4 rounded-lg shadow-sm border border-gray-200">
          <p className="text-sm text-gray-600">Total Tasks</p>
          <p className="text-2xl font-bold text-gray-900">{stats.total}</p>
        </div>
        <div className="bg-white p-4 rounded-lg shadow-sm border border-gray-200">
          <p className="text-sm text-gray-600">Active</p>
          <p className="text-2xl font-bold text-indigo-600">{stats.active}</p>
        </div>
        <div className="bg-white p-4 rounded-lg shadow-sm border border-gray-200">
          <p className="text-sm text-gray-600">Completed</p>
          <p className="text-2xl font-bold text-green-600">{stats.completed}</p>
        </div>
      </div>

      {/* Filters and Sort */}
      <div className="bg-white p-4 rounded-lg shadow-sm border border-gray-200 mb-6">
        <div className="flex flex-col sm:flex-row sm:items-center sm:justify-between space-y-3 sm:space-y-0">
          <div className="flex items-center space-x-2">
            <span className="text-sm font-medium text-gray-700">Filter:</span>
            <div className="flex space-x-1">
              {['all', 'active', 'completed'].map((f) => (
                <button
                  key={f}
                  onClick={() => setFilter(f)}
                  className={`px-3 py-1 text-sm rounded-md ${
                    filter === f
                      ? 'bg-indigo-600 text-white'
                      : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                  }`}
                >
                  {f.charAt(0).toUpperCase() + f.slice(1)}
                </button>
              ))}
            </div>
          </div>

          <div className="flex items-center space-x-2">
            <span className="text-sm font-medium text-gray-700">Sort by:</span>
            <select
              value={sortBy}
              onChange={(e) => setSortBy(e.target.value)}
              className="px-3 py-1 text-sm border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            >
              <option value="date">Date</option>
              <option value="priority">Priority</option>
              <option value="title">Title</option>
            </select>
          </div>
        </div>
      </div>

      {/* Task List */}
      {filteredAndSortedTasks.length === 0 ? (
        <div className="text-center py-12 bg-white rounded-lg shadow-sm border border-gray-200">
          <svg
            className="mx-auto h-12 w-12 text-gray-400"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={2}
              d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"
            />
          </svg>
          <h3 className="mt-2 text-sm font-medium text-gray-900">No tasks found</h3>
          <p className="mt-1 text-sm text-gray-500">
            {filter === 'all'
              ? 'Get started by creating a new task.'
              : `No ${filter} tasks.`}
          </p>
        </div>
      ) : (
        <div className="space-y-3">
          {filteredAndSortedTasks.map((task) => (
            <TaskItem key={task.id} task={task} />
          ))}
        </div>
      )}
    </div>
  );
}
```

## **Segment 6: Layout Components**

### **Navbar Component (src/components/layout/Navbar.jsx)**
```js
// src/components/layout/Navbar.jsx
import { useNavigate } from 'react-router-dom';
import { useAuth } from '../../contexts/AuthContext';
import { useTask } from '../../contexts/TaskContext';

export default function Navbar() {
  const { currentUser, logout } = useAuth();
  const { refreshTasks } = useTask();
  const navigate = useNavigate();

  const handleLogout = async () => {
    try {
      await logout();
      navigate('/login');
    } catch (err) {
      console.error('Failed to logout:', err);
    }
  };

  const handleRefresh = async () => {
    try {
      await refreshTasks();
    } catch (err) {
      console.error('Failed to refresh tasks:', err);
    }
  };

  return (
    <nav className="bg-white shadow-lg">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between h-16">
          <div className="flex items-center">
            <div className="flex-shrink-0 flex items-center">
              <svg
                className="h-8 w-8 text-indigo-600"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  strokeLinecap="round"
                  strokeLinejoin="round"
                  strokeWidth={2}
                  d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4"
                />
              </svg>
              <span className="ml-2 text-xl font-bold text-gray-900">TaskManager</span>
            </div>
          </div>

          <div className="flex items-center space-x-4">
            {currentUser && (
              <>
                <button
                  onClick={handleRefresh}
                  className="p-2 text-gray-600 hover:text-indigo-600 focus:outline-none"
                  title="Refresh tasks"
                >
                  <svg
                    className="h-5 w-5"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                  >
                    <path
                      strokeLinecap="round"
                      strokeLinejoin="round"
                      strokeWidth={2}
                      d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                    />
                  </svg>
                </button>

                <div className="flex items-center space-x-3">
                  <div className="text-right hidden sm:block">
                    <p className="text-sm font-medium text-gray-900">
                      {currentUser.displayName || 'User'}
                    </p>
                    <p className="text-xs text-gray-500">{currentUser.email}</p>
                  </div>
                  
                  <div className="h-10 w-10 rounded-full bg-indigo-600 flex items-center justify-center text-white font-semibold">
                    {(currentUser.displayName || currentUser.email || 'U')[0].toUpperCase()}
                  </div>
                </div>

                <button
                  onClick={handleLogout}
                  className="bg-red-600 text-white px-4 py-2 rounded-md hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition-colors"
                >
                  Logout
                </button>
              </>
            )}
          </div>
        </div>
      </div>
    </nav>
  );
}
```

### **Dashboard Component (src/components/layout/Dashboard.jsx)**---
```js
// src/components/layout/Dashboard.jsx
import Navbar from './Navbar';
import TaskForm from '../tasks/TaskForm';
import TaskList from '../tasks/TaskList';
import { TaskProvider } from '../../contexts/TaskContext';

export default function Dashboard() {
  return (
    <TaskProvider>
      <div className="min-h-screen bg-gray-50">
        <Navbar />
        
        <main className="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
          <div className="mb-6">
            <h1 className="text-3xl font-bold text-gray-900">My Tasks</h1>
            <p className="mt-2 text-sm text-gray-600">
              Manage your daily tasks efficiently
            </p>
          </div>

          <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Task Form - Left Column */}
            <div className="lg:col-span-1">
              <TaskForm />
            </div>

            {/* Task List - Right Column */}
            <div className="lg:col-span-2">
              <TaskList />
            </div>
          </div>
        </main>
      </div>
    </TaskProvider>
  );
}
```

## **Segment 7: Main App Files**

### **App.jsx (src/App.jsx)**
```js
// src/App.jsx
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import { AuthProvider } from './contexts/AuthContext';
import Login from './components/auth/Login';
import Dashboard from './components/layout/Dashboard';
import PrivateRoute from './components/auth/PrivateRoute';

function App() {
  return (
    <Router>
      <AuthProvider>
        <Routes>
          <Route path="/login" element={<Login />} />
          <Route
            path="/dashboard"
            element={
              <PrivateRoute>
                <Dashboard />
              </PrivateRoute>
            }
          />
          <Route path="/" element={<Navigate to="/dashboard" replace />} />
          <Route path="*" element={<Navigate to="/dashboard" replace />} />
        </Routes>
      </AuthProvider>
    </Router>
  );
}

export default App;
```

### **main.jsx (src/main.jsx)**
```js
// src/main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App.jsx';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### **index.css (src/index.css)**---
```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  line-height: 1.5;
  font-weight: 400;

  color-scheme: light dark;
  color: rgba(0, 0, 0, 0.87);
  background-color: #ffffff;

  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

body {
  margin: 0;
  min-height: 100vh;
}

#root {
  min-height: 100vh;
}
```

## **Segment 8: Configuration Files**

### **package.json**
```json
{
  "name": "task-manager-app",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext js,jsx --report-unused-disable-directives --max-warnings 0"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.22.0",
    "firebase": "^10.8.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.55",
    "@types/react-dom": "^18.2.19",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.17",
    "eslint": "^8.56.0",
    "eslint-plugin-react": "^7.33.2",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.5",
    "postcss": "^8.4.35",
    "tailwindcss": "^3.4.1",
    "vite": "^5.1.0"
  }
}
```

### **vite.config.js**
```js
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    open: true
  }
});
```

### **tailwind.config.js**
```js
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### **postcss.config.js**
```js
// postcss.config.js
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### **.gitignore**
```.git
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

node_modules
dist
dist-ssr
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Environment variables
.env
.env.local
.env.production
```

### **index.html (public/index.html or root)**---
```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Task Manager - Organize Your Tasks</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

## **Segment 9: Setup Instructions & Documentation**---
READme.md
```mackdown
# Task Manager App - React + Firebase

A complete task management application built with React (Vite), Firebase v10+, and Tailwind CSS featuring optimistic UI updates for a real-time feel.

## Features

- ✅ **Authentication**: Email/Password and Google Sign-in
- ✅ **Task Management**: Create, Read, Update, Delete tasks
- ✅ **Optimistic UI**: Instant updates before Firebase confirmation
- ✅ **User Data Isolation**: Strict Firestore security rules
- ✅ **Filtering & Sorting**: Filter by status, sort by date/priority/title
- ✅ **Responsive Design**: Mobile-friendly Tailwind UI
- ✅ **Priority Levels**: Low, Medium, High priority tasks

## Tech Stack

- **Frontend**: React 18 with Vite
- **Authentication**: Firebase Authentication v10
- **Database**: Cloud Firestore v10
- **State Management**: React Context API
- **Styling**: Tailwind CSS
- **Routing**: React Router v6

## Project Structure

```
task-manager-app/
├── src/
│   ├── components/
│   │   ├── auth/           # Authentication components
│   │   ├── tasks/          # Task-related components
│   │   └── layout/         # Layout components
│   ├── contexts/           # React Context providers
│   ├── services/           # Firebase services
│   ├── hooks/              # Custom hooks
│   └── App.jsx
├── .env                    # Environment variables
└── package.json
```

## Setup Instructions

### 1. Clone and Install

```bash
# Clone the repository
git clone <your-repo-url>
cd task-manager-app

# Install dependencies
npm install
```

### 2. Firebase Setup

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Create a new project
3. Enable **Authentication**:
   - Go to Authentication > Sign-in method
   - Enable Email/Password
   - Enable Google
4. Create **Firestore Database**:
   - Go to Firestore Database
   - Create database in production mode
   - Deploy the security rules (provided in artifacts)
5. Get your Firebase config:
   - Go to Project Settings > General
   - Scroll to "Your apps" section
   - Copy the Firebase configuration

### 3. Configure Environment Variables

Create a `.env` file in the root directory:

```env
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your_project_id
VITE_FIREBASE_STORAGE_BUCKET=your_project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
VITE_FIREBASE_APP_ID=your_app_id
```

### 4. Deploy Firestore Security Rules

In Firebase Console:
1. Go to Firestore Database > Rules
2. Copy and paste the security rules from the artifact
3. Publish the rules

### 5. Run the Application

```bash
# Development mode
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

The app will open at `http://localhost:3000`

## Firebase Security Rules

The app implements strict user data isolation:
- Users can only read their own tasks
- Users can only create tasks with their own userId
- Users can only update/delete their own tasks
- All other access is denied by default

## Key Features Explanation

### Optimistic UI Updates

The app uses optimistic updates for instant feedback:

1. **Add Task**: Task appears immediately, then syncs with Firebase
2. **Update Task**: Changes show instantly, rollback on error
3. **Delete Task**: Removed immediately, restored on error

This is implemented in `TaskContext.jsx` using React state management.

### User Data Isolation

Firestore security rules ensure:
- Each task has a `userId` field matching the creator
- Queries filter by authenticated user's UID
- Server-side validation prevents unauthorized access

### State Management

- **AuthContext**: Manages authentication state globally
- **TaskContext**: Manages task state with optimistic updates
- No real-time listeners (onSnapshot) used per requirements

## Usage

1. **Sign Up/Login**: Create account or sign in with Google
2. **Add Tasks**: Fill the form and click "Add Task"
3. **Manage Tasks**: 
   - Click checkbox to mark complete
   - Click edit icon to modify
   - Click delete icon to remove
4. **Filter**: View all, active, or completed tasks
5. **Sort**: By date, priority, or title

## Architecture Decisions

### Why No onSnapshot?

Per requirements, we use centralized state instead of real-time listeners. This approach:
- Reduces Firebase read operations
- Provides full control over data flow
- Implements optimistic UI for perceived real-time updates

### Modular Structure

- Services layer abstracts Firebase operations
- Context providers manage global state
- Components focus on UI presentation
- Clean separation of concerns

## Development Tips

- Use React DevTools to inspect context state
- Check Firebase Console for Firestore data
- Monitor Authentication tab for user sessions
- Use browser console for debugging

## Production Deployment

1. Build the app: `npm run build`
2. Deploy to Firebase Hosting:
   ```bash
   npm install -g firebase-tools
   firebase login
   firebase init hosting
   firebase deploy
   ```

Or deploy to Vercel/Netlify by connecting your repository.

## Troubleshooting

**Issue**: Firebase errors on load
- **Solution**: Check `.env` file has correct credentials

**Issue**: Can't create tasks
- **Solution**: Verify Firestore rules are deployed correctly

**Issue**: Optimistic updates not working
- **Solution**: Check browser console for errors in TaskContext

## License

MIT

## Contributing

Feel free to submit issues and pull requests!


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
---
---
---
# Complete Firestore Configuration Guide

## Step-by-Step Firestore Setup

### Step 1: Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click **"Add project"** or **"Create a project"**
3. Enter project name (e.g., "task-manager-app")
4. Click **Continue**
5. Choose whether to enable Google Analytics (optional)
6. Click **Create project**
7. Wait for project creation, then click **Continue**

---

### Step 2: Enable Firestore Database

1. In Firebase Console, click **"Firestore Database"** in the left sidebar
2. Click **"Create database"**
3. **Select Starting Mode**:
   - Choose **"Start in production mode"** (we'll add custom rules next)
   - Click **Next**
4. **Choose Location**:
   - Select a Cloud Firestore location closest to your users
   - **IMPORTANT**: This cannot be changed later!
   - Common choices:
     - `us-central1` (United States)
     - `europe-west1` (Belgium)
     - `asia-southeast1` (Singapore)
   - Click **Enable**
5. Wait for Firestore to be provisioned (takes 1-2 minutes)

---

### Step 3: Configure Firestore Security Rules

#### Option A: Using Firebase Console (Recommended for Beginners)

1. In Firestore Database, click the **"Rules"** tab at the top
2. You'll see the default rules. **Delete everything** in the editor
3. Copy the rules from the artifact I provided earlier:

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper function to check if user is authenticated
    function isAuthenticated() {
      return request.auth != null;
    }
    
    // Helper function to check if user owns the document
    function isOwner(userId) {
      return isAuthenticated() && request.auth.uid == userId;
    }
    
    // Tasks collection - strict user isolation
    match /tasks/{taskId} {
      // Users can only read their own tasks
      allow read: if isAuthenticated() && 
                     resource.data.userId == request.auth.uid;
      
      // Users can only create tasks with their own userId
      allow create: if isAuthenticated() && 
                       request.resource.data.userId == request.auth.uid &&
                       request.resource.data.keys().hasAll(['title', 'userId', 'createdAt']) &&
                       request.resource.data.userId is string &&
                       request.resource.data.title is string;
      
      // Users can only update their own tasks
      allow update: if isAuthenticated() && 
                       resource.data.userId == request.auth.uid &&
                       request.resource.data.userId == request.auth.uid;
      
      // Users can only delete their own tasks
      allow delete: if isAuthenticated() && 
                       resource.data.userId == request.auth.uid;
    }
    
    // Deny all other access by default
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

4. Click **"Publish"** button
5. Confirm the publication

#### Option B: Using Firebase CLI (Advanced)

1. Install Firebase CLI:
```bash
npm install -g firebase-tools
```

2. Login to Firebase:
```bash
firebase login
```

3. Initialize Firestore in your project:
```bash
firebase init firestore
```

4. Create `firestore.rules` file in your project root with the rules above

5. Deploy the rules:
```bash
firebase deploy --only firestore:rules
```

---

### Step 4: Create Firestore Indexes (Optional but Recommended)

Our app queries tasks with `where` and `orderBy` clauses. Firebase may require composite indexes.

#### Create Index for Tasks Collection:

1. Go to **Firestore Database** > **Indexes** tab
2. Click **"Create Index"**
3. Configure the index:
   - **Collection ID**: `tasks`
   - **Fields to index**:
     - Field: `userId`, Order: `Ascending`
     - Field: `createdAt`, Order: `Descending`
   - **Query scope**: Collection
4. Click **"Create index"**
5. Wait for index to build (usually 1-2 minutes)

**Alternative**: Firebase will automatically suggest creating indexes when you first run queries that need them. You can create them on-demand through the error messages.

---

### Step 5: Enable Authentication

1. In Firebase Console, click **"Authentication"** in the left sidebar
2. Click **"Get started"**
3. Go to **"Sign-in method"** tab

#### Enable Email/Password:
1. Click **"Email/Password"**
2. Toggle **Enable** to ON
3. (Optional) Toggle "Email link (passwordless sign-in)" if desired
4. Click **Save**

#### Enable Google Sign-In:
1. Click **"Google"**
2. Toggle **Enable** to ON
3. Select a **Project support email** (your email)
4. Click **Save**

---

### Step 6: Get Firebase Configuration

1. In Firebase Console, click the **⚙️ gear icon** (Settings) next to "Project Overview"
2. Click **"Project settings"**
3. Scroll down to **"Your apps"** section
4. Click the **Web icon** (`</>`) to add a web app
5. Register app:
   - **App nickname**: "Task Manager Web App"
   - (Optional) Check "Also set up Firebase Hosting"
   - Click **"Register app"**
6. Copy the Firebase configuration object:

```javascript
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

7. Click **"Continue to console"**

---

### Step 7: Configure Environment Variables

1. In your project root, create a `.env` file
2. Add your Firebase credentials:

```env
VITE_FIREBASE_API_KEY=AIza...
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abc123
```

**IMPORTANT**: 
- Replace all values with YOUR actual Firebase config
- Never commit `.env` to version control (it's in `.gitignore`)
- For production, set these as environment variables in your hosting platform

---

### Step 8: Verify Firestore Setup

#### Test Database Connection:

1. Start your app:
```bash
npm run dev
```

2. Sign up with a new account
3. Create a test task
4. Go to Firebase Console > Firestore Database > Data tab
5. You should see:
   - Collection: `tasks`
   - Document ID: auto-generated
   - Fields: `title`, `description`, `userId`, `completed`, `priority`, `createdAt`, `updatedAt`

#### Verify Security Rules:

1. In Firestore Console, click **Rules** tab
2. Click **"Rules playground"**
3. Test a read operation:
   - **Location**: `/tasks/test-doc-id`
   - **Authenticated**: Yes
   - **Provider**: Email
   - Click **"Run"**
4. Should show "Simulated read allowed" ✅

---

## Firestore Data Structure

Your Firestore database will have this structure:

```
firestore (root)
└── tasks (collection)
    ├── {taskId1} (document)
    │   ├── title: "Complete project"
    │   ├── description: "Finish the task manager app"
    │   ├── userId: "user123abc"
    │   ├── completed: false
    │   ├── priority: "high"
    │   ├── createdAt: Timestamp
    │   └── updatedAt: Timestamp
    ├── {taskId2} (document)
    │   └── ...
    └── {taskId3} (document)
        └── ...
```

---

## Important Notes

### Security Best Practices:

✅ **DO:**
- Always validate `userId` matches authenticated user
- Use server timestamps (`serverTimestamp()`)
- Implement proper field validation in rules
- Keep API keys in environment variables
- Use production mode with custom rules

❌ **DON'T:**
- Never use test mode in production
- Don't allow open read/write access
- Don't commit `.env` files to Git
- Don't disable authentication checks

### Cost Optimization:

- **Free Tier Limits**:
  - 50,000 reads/day
  - 20,000 writes/day
  - 20,000 deletes/day
  - 1 GB storage

- **Optimization Tips**:
  - Use optimistic UI (already implemented)
  - Cache data in React state
  - Avoid unnecessary queries
  - Use pagination for large lists

---

## Troubleshooting

### Problem: "Missing or insufficient permissions"

**Solution**: 
- Check Firestore rules are deployed correctly
- Verify user is authenticated
- Ensure `userId` field matches authenticated user

### Problem: "PERMISSION_DENIED: Missing or insufficient permissions"

**Solution**:
- Go to Firestore Rules
- Verify rules are published
- Check rules syntax (no errors)
- Test in Rules Playground

### Problem: "Failed to get document because the client is offline"

**Solution**:
- Check internet connection
- Verify Firebase config in `.env`
- Clear browser cache and reload

### Problem: Indexes taking too long

**Solution**:
- Check Firestore > Indexes tab
- Look for "Building" status
- Usually takes 1-5 minutes max
- Create indexes manually if auto-creation fails

---

## Next Steps

After configuring Firestore:

1. ✅ Test authentication (sign up/login)
2. ✅ Create a few tasks
3. ✅ Verify tasks appear in Firestore Console
4. ✅ Test filtering and sorting
5. ✅ Test security (try accessing another user's tasks - should fail)
6. ✅ Deploy to production

---

## Quick Reference Commands

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login to Firebase
firebase login

# Initialize Firestore
firebase init firestore

# Deploy rules
firebase deploy --only firestore:rules

# Deploy app to Firebase Hosting
firebase deploy --only hosting
```

---

