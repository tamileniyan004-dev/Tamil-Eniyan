# DrawTrack Pro — Setup Guide
## Google Login + Real-Time Sync via Firebase

---

## Step 1 — Create a Firebase project

1. Go to https://console.firebase.google.com
2. Click **Add project** → name it (e.g. `drawtrack-pro`)
3. Disable Google Analytics (optional) → **Create project**

---

## Step 2 — Enable Google Authentication

1. In Firebase Console → **Build → Authentication → Get started**
2. Click **Google** under Sign-in providers → Enable → Save
3. Set your **support email** (required by Google)

---

## Step 3 — Create Firestore database

1. Firebase Console → **Build → Firestore Database → Create database**
2. Choose **Start in production mode**
3. Pick a region (e.g. `asia-south1` for India/Middle East)

---

## Step 4 — Deploy Firestore security rules

```bash
npm install -g firebase-tools
firebase login
firebase init firestore   # select your project
# Copy firestore.rules content → deploy
firebase deploy --only firestore:rules
```

---

## Step 5 — Register a web app & get config

1. Firebase Console → Project Settings (gear icon) → **Your apps** → **</>** Web
2. Register app name → Copy the `firebaseConfig` object
3. Open `src/lib/firebase.js` and paste your config values

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "your-app.firebaseapp.com",
  projectId: "your-app",
  storageBucket: "your-app.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123:web:abc",
};
```

---

## Step 6 — Add your domain to Authorized Domains

1. Firebase Console → Authentication → **Settings → Authorized domains**
2. Add `localhost` (for dev) and your production domain

---

## Step 7 — Install & run locally

```bash
cd drawtrack-pro
npm install
npm run dev
# Opens at http://localhost:5173
```

---

## Step 8 — Deploy to the web (Vercel — free)

```bash
npm install -g vercel
vercel        # follow prompts, paste env vars if needed
```

Or connect your GitHub repo to https://vercel.com for auto-deploy on every push.

---

## How real-time sync works

Every connected user gets **live updates via Firestore's `onSnapshot` listeners**:

- When Engineer A updates a revision → Engineer B sees it instantly (no refresh)
- When Admin adds a villa → all users see the new column appear
- Activity log updates live for all project members
- Each update is stamped with the user's name and time

---

## User roles

| Role | Can do |
|------|--------|
| **engineer** | View projects they're a member of, update revisions, add drawings |
| **admin** | Everything above + delete projects, promote users |

To make someone an admin, go to **Firestore → users → {uid} → Edit → role: "admin"**

---

## Folder structure

```
drawtrack-pro/
├── src/
│   ├── lib/
│   │   ├── firebase.js      ← Firebase init (put your config here)
│   │   ├── db.js            ← All Firestore read/write helpers
│   │   └── AuthContext.jsx  ← Google login context
│   ├── hooks/
│   │   └── useRealtimeData.js ← Live data hooks
│   ├── components/
│   │   └── Dashboard.jsx    ← Revision grid
│   ├── pages/
│   │   └── LoginPage.jsx    ← Google sign-in screen
│   ├── App.jsx              ← Main app shell
│   ├── main.jsx             ← React entry point
│   └── index.css            ← Styles
├── firestore.rules          ← Security rules (deploy to Firebase)
├── index.html
├── vite.config.js
└── package.json
```

---

## Optional next steps

- **File uploads** (PDF/DWG): Enable Firebase Storage, add upload button in revision modal
- **Email notifications**: Use Firebase Cloud Functions to trigger emails on revision updates
- **Invite team members**: Add a "Share project" button that adds a user UID to `project.members[]`
- **Mobile app**: Same Firebase backend works with React Native
