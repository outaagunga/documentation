
 **React + Firestore version** of this WordPress gated investor directory. Let’s translate your WordPress system architecture and workflow into a modern React/Firebase setup step by step. I’ll map every concept and segment so it’s no-guesswork.

---

# 🧱 SEGMENT 1 — Core Architecture in React + Firestore

## 1️⃣ Data Model

In Firestore, each **Investor** will be a document in a collection. Users are **separate documents** in a `users` collection.

**Collections & Documents:**

```
users (collection)
  userId (document)
    - name
    - email
    - role: "guest" | "free" | "premium" | "admin"
    - subscriptionStatus
    - createdAt

investors (collection)
  investorId (document)
    - investorName
    - firmName
    - industryFocus
    - investmentStage
    - country
    - website
    - email
    - phone
    - whatsapp
    - linkedin
    - verified: boolean
    - featured: boolean
    - createdAt
    - updatedAt

taxonomies (collection)
  industry (document)
    - name
    - slug
  country (document)
    - name
    - slug
```

✅ Firestore advantage: No separate plugin needed — everything is structured, indexed, and scalable.

---

## 2️⃣ User Roles & Permissions

Create roles in your `users` documents:

| Role    | Can See                                            |
| ------- | -------------------------------------------------- |
| Guest   | Public fields only (name, firm, country, industry) |
| Free    | + Email                                            |
| Premium | + Phone, WhatsApp, LinkedIn                        |
| Admin   | Everything + edit/create/delete rights             |

**Implementation:** Role-based conditional rendering in React components.

---

## 3️⃣ Routing & Access Control

Using **React Router**, your pages map directly to URLs:

| URL                  | Page Component     | Access Control                              |
| -------------------- | ------------------ | ------------------------------------------- |
| `/investors`         | InvestorsList      | Public                                      |
| `/investor/:id`      | InvestorProfile    | Guests see teaser, logged-in users see full |
| `/dashboard`         | AdminDashboard     | Admin only                                  |
| `/add-investor`      | AddInvestorForm    | Admin only                                  |
| `/edit-investor/:id` | EditInvestorForm   | Admin only                                  |
| `/submit-fund`       | InvestorSubmitForm | Public (creates draft)                      |
| `/upgrade`           | UpgradePage        | Free → Premium                              |

---

## 4️⃣ Frontend Admin System

Admins should be able to add/edit investors **without backend login**.

**Components:**

1. **AdminDashboard.jsx**

   * Shows a list of investors.
   * Buttons: "Add Investor", "Edit", "Approve".

2. **AddInvestorForm.jsx**

   * Form fields: all investor fields except `verified` and `featured`.
   * On submit → Firestore `investors` collection → `draft` status.

3. **EditInvestorForm.jsx**

   * Load investor by `id`.
   * Update Firestore document.
   * Only accessible by admins.

4. **Approval Workflow**

   * Admin toggles `verified: true`.
   * Only verified investors appear in public listing.

**Firestore rules:**

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    match /investors/{investorId} {
      allow read: if resource.data.verified == true || request.auth.token.role == 'admin';
      allow create, update, delete: if request.auth.token.role == 'admin';
    }
    
    match /users/{userId} {
      allow read, update: if request.auth.uid == userId || request.auth.token.role == 'admin';
    }
  }
}
```

---

## 5️⃣ Conditional Rendering in React

Example **InvestorProfile.jsx**:

```jsx
import { useAuth } from './AuthContext';
import { doc, getDoc } from 'firebase/firestore';
import { db } from './firebase';

function InvestorProfile({ investorId }) {
  const { user } = useAuth();
  const [investor, setInvestor] = React.useState(null);

  React.useEffect(() => {
    async function fetchInvestor() {
      const docSnap = await getDoc(doc(db, 'investors', investorId));
      if (docSnap.exists()) setInvestor(docSnap.data());
    }
    fetchInvestor();
  }, [investorId]);

  if (!investor) return <div>Loading...</div>;

  return (
    <div>
      <h1>{investor.investorName}</h1>
      <p>{investor.firmName}</p>
      <p>{investor.industryFocus} - {investor.country}</p>
      <p>Website: <a href={investor.website}>{investor.website}</a></p>

      {user?.role === 'admin' || user?.role === 'premium' ? (
        <>
          <p>Email: {investor.email}</p>
          <p>Phone: {investor.phone}</p>
          <p>WhatsApp: {investor.whatsapp}</p>
          <p>LinkedIn: {investor.linkedin}</p>
        </>
      ) : user?.role === 'free' ? (
        <p>Email: {investor.email}</p>
      ) : (
        <p>🔒 Login or Upgrade to see contact info</p>
      )}

      {user?.role === 'admin' && (
        <button onClick={() => navigate(`/edit-investor/${investorId}`)}>Edit Investor</button>
      )}
    </div>
  );
}
```

---

## 6️⃣ Search, Filter & Directory Pages

Use **Firestore queries** to implement filtering:

```javascript
import { collection, query, where, getDocs } from 'firebase/firestore';

async function getInvestors(filters) {
  let q = collection(db, 'investors');
  
  if (filters.industry) q = query(q, where('industryFocus', '==', filters.industry));
  if (filters.country) q = query(q, where('country', '==', filters.country));
  if (filters.verified !== undefined) q = query(q, where('verified', '==', filters.verified));

  const snapshot = await getDocs(q);
  return snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
}
```

* `/investors` → fetch all `verified: true`.
* `/industry/:slug` → filter by `industryFocus`.
* `/country/:slug` → filter by `country`.

---

## 7️⃣ Self-Submission & Verification

* Public investors fill **InvestorSubmitForm.jsx**.
* Submits document with `verified: false`.
* Admin sees pending list on dashboard → approves → sets `verified: true`.
* Optional: Google reCAPTCHA for spam prevention.

---


Perfect — let’s design a **complete, production-ready React + Firebase project structure** for your gated investor directory. This mirrors the WordPress workflow you shared, but built natively with **React + Firestore + Firebase Auth**. I’ll include **folders, key components, pages, and Firebase collections**, plus a clear outline of responsibilities.

---

# 🏗️ Project Structure

```
investor-directory/
├─ public/
│  └─ index.html
├─ src/
│  ├─ api/
│  │  ├─ firebase.js           # Firebase config & initialization
│  │  ├─ auth.js               # Auth helper functions
│  │  └─ investorService.js    # Firestore CRUD queries for investors
│  ├─ components/
│  │  ├─ layout/
│  │  │  ├─ Header.jsx
│  │  │  ├─ Footer.jsx
│  │  │  └─ PrivateRoute.jsx   # Route protection based on role
│  │  ├─ investors/
│  │  │  ├─ InvestorCard.jsx
│  │  │  ├─ InvestorList.jsx
│  │  │  └─ InvestorProfile.jsx
│  │  ├─ admin/
│  │  │  ├─ AdminDashboard.jsx
│  │  │  ├─ AddInvestorForm.jsx
│  │  │  ├─ EditInvestorForm.jsx
│  │  │  └─ PendingInvestors.jsx
│  │  └─ ui/
│  │     ├─ Button.jsx
│  │     ├─ Modal.jsx
│  │     └─ Loader.jsx
│  ├─ context/
│  │  └─ AuthContext.jsx       # Auth + role context provider
│  ├─ hooks/
│  │  └─ useFetchInvestors.js
│  ├─ pages/
│  │  ├─ Home.jsx
│  │  ├─ InvestorsPage.jsx
│  │  ├─ InvestorPage.jsx
│  │  ├─ IndustryPage.jsx
│  │  ├─ CountryPage.jsx
│  │  ├─ Login.jsx
│  │  ├─ Register.jsx
│  │  ├─ Upgrade.jsx
│  │  ├─ Dashboard.jsx
│  │  ├─ AddInvestor.jsx
│  │  ├─ EditInvestor.jsx
│  │  └─ SubmitFund.jsx
│  ├─ styles/
│  │  ├─ globals.css
│  │  └─ components.css
│  ├─ App.jsx
│  └─ index.js
├─ .env.local                  # Firebase keys & config
├─ package.json
└─ README.md
```

---

# 🔹 Firebase Collections & Structure

**Firestore** collections:

```
users (collection)
  userId (document)
    - name
    - email
    - role: "guest" | "free" | "premium" | "admin"
    - subscriptionStatus
    - createdAt

investors (collection)
  investorId (document)
    - investorName
    - firmName
    - industryFocus
    - investmentStage
    - country
    - website
    - email
    - phone
    - whatsapp
    - linkedin
    - verified: boolean
    - featured: boolean
    - createdAt
    - updatedAt

taxonomies (collection)
  industries (document)
    - name
    - slug
  countries (document)
    - name
    - slug
```

**Firebase Auth:**

* Email/password authentication for users.
* User roles stored in Firestore (`users` collection).
* Role used for conditional rendering & route protection.

**Firestore Rules:**

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /investors/{investorId} {
      allow read: if resource.data.verified == true || request.auth.token.role == 'admin';
      allow create, update, delete: if request.auth.token.role == 'admin';
    }

    match /users/{userId} {
      allow read, update: if request.auth.uid == userId || request.auth.token.role == 'admin';
    }
  }
}
```

---

# 🔹 Key React Components & Responsibilities

| Component / Page                       | Responsibility                                    |
| -------------------------------------- | ------------------------------------------------- |
| **Header / Footer**                    | Navigation, links, login/signup buttons           |
| **PrivateRoute.jsx**                   | Protects routes based on `role`                   |
| **InvestorCard.jsx**                   | Shows public info in listings                     |
| **InvestorList.jsx**                   | Fetches investors from Firestore + filters        |
| **InvestorProfile.jsx**                | Full profile page with gated contact info         |
| **AdminDashboard.jsx**                 | Admin overview: pending investors, stats          |
| **AddInvestorForm.jsx**                | Form to create new investors (Admin only)         |
| **EditInvestorForm.jsx**               | Edit existing investors (Admin only)              |
| **PendingInvestors.jsx**               | List of unverified investors awaiting approval    |
| **Home.jsx**                           | Hero section, featured investors, CTA buttons     |
| **InvestorsPage.jsx**                  | Public directory listing, searchable + filterable |
| **IndustryPage.jsx / CountryPage.jsx** | Filtered directory pages                          |
| **SubmitFund.jsx**                     | Investor self-submission form (draft)             |
| **Upgrade.jsx**                        | Freemium → Premium subscription UI                |

---

# 🔹 React Context & Hooks

* `AuthContext.jsx` → Provides user info, role, and login status.
* `useFetchInvestors.js` → Custom hook to query Firestore with optional filters.
* Conditional rendering based on role (guest, free, premium, admin).

---

# 🔹 Routing

```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import PrivateRoute from './components/layout/PrivateRoute';

<BrowserRouter>
  <Header />
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/investors" element={<InvestorsPage />} />
    <Route path="/investor/:id" element={<InvestorPage />} />
    <Route path="/industry/:slug" element={<IndustryPage />} />
    <Route path="/country/:slug" element={<CountryPage />} />
    <Route path="/login" element={<Login />} />
    <Route path="/register" element={<Register />} />
    <Route path="/submit-fund" element={<SubmitFund />} />

    {/* Admin Protected */}
    <Route element={<PrivateRoute role="admin" />}>
      <Route path="/dashboard" element={<Dashboard />} />
      <Route path="/add-investor" element={<AddInvestor />} />
      <Route path="/edit-investor/:id" element={<EditInvestor />} />
    </Route>

    {/* Premium protected */}
    <Route element={<PrivateRoute role="premium" />}>
      <Route path="/upgrade" element={<Upgrade />} />
    </Route>
  </Routes>
  <Footer />
</BrowserRouter>
```

---

# 🔹 Filter & Search System

* Filters by country, industry, stage, featured.
* Search bar searches `investorName`, `firmName`.
* Firestore queries use `where` clauses for filtering.
* Conditional rendering hides sensitive data for guests/free users.

---

# 🔹 Monetization & Access Control

* Roles stored in Firestore (`free`, `premium`).
* Conditional rendering on profiles to restrict contact info.
* Upgrade page integrates **Stripe / Firebase Functions** for payments.

---

# 🔹 Self-Submission Flow

1. Public submits fund → Firestore `investors` document created with `verified: false`.
2. Admin sees pending list → approves → sets `verified: true`.
3. Optional: redirect to upgrade page to become featured.

---

✅ With this structure, you have a **complete React + Firebase blueprint** for a gated, monetizable investor directory, fully replacing the WordPress logic.

---
