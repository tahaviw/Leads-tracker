# Leads Tracker — Chrome Extension

A Manifest V3 Chrome extension for saving and managing URLs across browsing sessions. All leads sync in real time via Firebase Realtime Database, meaning data persists across devices and browser restarts without touching localStorage.

---

## You can view the live demo of the project here: https://leads-tracker-tahaviw.netlify.app/

## Features

- **Save any URL** manually via text input
- **Real-time sync** — leads update instantly via Firebase `onValue` listener (no polling)
- **Persistent storage** — data survives tab closes, browser restarts, and device switches
- **Double-click delete** — removes all leads from the database and clears the UI
- **Clickable links** — each saved lead renders as an anchor tag opening in a new tab
- **Manifest V3** compliant — uses `action.default_popup`, compatible with current Chrome extension standards

---

## Tech Stack

| Layer      | Technology                                      |
|------------|-------------------------------------------------|
| Markup     | HTML5                                           |
| Styling    | CSS3                                            |
| Logic      | Vanilla JavaScript (ES Modules)                 |
| Database   | Firebase Realtime Database (v12, CDN via ESM)   |
| Platform   | Chrome Extension — Manifest V3                  |

---

## Setup

**Prerequisites:** A [Firebase project](https://console.firebase.google.com/) with Realtime Database enabled (start in test mode for local dev).

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/leads-tracker.git
cd leads-tracker
```

**2. Configure Firebase**

Create a `config.js` file in the project root and export your Firebase config object:

```js
// config.js
export const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT-default-rtdb.firebaseio.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
}
```

> `config.js` should be added to `.gitignore` to avoid exposing credentials.

**3. Load the extension in Chrome**

1. Open `chrome://extensions/`
2. Enable **Developer mode** (top right toggle)
3. Click **Load unpacked**
4. Select the project root folder

The extension icon will appear in the Chrome toolbar.

---

## Usage

| Action | How |
|--------|-----|
| Save a URL | Type or paste a URL → click **SAVE INPUT** |
| View saved leads | Leads render automatically as clickable links |
| Open a lead | Click any link — opens in a new tab |
| Delete all leads | **Double-click** the **DELETE ALL** button |

> Double-click on delete is intentional — prevents accidental data loss.

---

## Architecture

```
index.html
│
├── index.css          # UI styling
└── index.js           # All application logic (ES Module)
    │
    ├── Firebase init  # initializeApp(firebaseConfig) → getDatabase()
    ├── ref()          # Points to "leads" node in the database
    │
    ├── onValue()      # Real-time listener — rerenders list on any DB change
    ├── push()         # Appends new lead on SAVE INPUT click
    └── remove()       # Wipes entire "leads" node on DELETE ALL double-click

config.js              # Firebase credentials (gitignored)
manifest.json          # Chrome Extension Manifest V3 config
```

**Data flow:**

```
User input → push(ref, value) → Firebase DB
Firebase DB → onValue() fires → render() → DOM update
```

The extension holds no local state. The Firebase `onValue` listener is the single source of truth — any write (push or remove) triggers a re-render automatically.

---

## Known Limitations

- **No input validation** — any string (including empty input) can be pushed to the database
- **No authentication** — database is open; anyone with the `databaseURL` can read/write in test mode. Set Firebase Security Rules before any public deployment
- **`config.js` must be gitignored** — committing it exposes your Firebase project to public access
- **`tabs` permission** declared in manifest but unused in current JS — either remove it or implement `chrome.tabs.query()` to save the active tab URL directly

---

## Author

**Taha Belghiti** — Junior Front-End Developer  
Built with HTML, CSS, JavaScript & Firebase · © 2026
