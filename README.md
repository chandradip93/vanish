# Aradhrika Personal Messaging App

> **Private, 24-Hour Ephemeral Messenger**
> Designed with modern chat UI, strict international phone number validation, user profiles stored in SQLite, and 24-hour disappearing messages, photos, and videos.

Built with **Native Node.js 24 + SQLite (`node:sqlite`) + Real-time Server-Sent Events (SSE)**. Zero external npm dependencies.

---

## 🌟 Key Features

### 1. 📱 Strict E.164 International Phone Number Validation
- Country code selector dropdown with country flags (🇮🇳 +91, 🇺🇸 +1, 🇬🇧 +44, 🇦🇺 +61, 🇩🇪 +49, 🇦🇪 +971, 🇸🇬 +65, 🇫🇷 +33, 🇯🇵 +81, or Custom country code).
- Rejects invalid numbers, non-numeric characters, and incorrect digit lengths (enforcing 10 digits for India/USA, 10-11 for UK, 8-15 digits globally).
- Simulated 6-digit SMS OTP verification (standard test code: `123456`).

### 2. 👤 User Registration & Persistent Database Storage
- **Step 1**: Phone number verification with country selector.
- **Step 2**: Enter 6-digit SMS OTP code.
- **Step 3 (Profile Setup)**:
  - **Full Name** (Required, saved in SQLite `users` table).
  - **About / Status** (e.g. *"Available"*, *"Busy"*, *"In a meeting"*).
  - **Avatar Color** picker with initials generator.
- Profiles are permanently stored in SQLite `vanish.db` and displayed across conversations and search results.

### 3. 💬 Modern Chat UI & Real-Time UX
- Signature teal header (`#008069`), green accents (`#00a884`), and subtle doodle chat wallpaper.
- Outgoing message bubble (`#D9FDD3`) and incoming message bubble (`#FFFFFF`).
- **Read Receipts with Checkmarks**:
  - Single gray check (`✓`): Sent to server
  - Double gray check (`✓✓`): Delivered to recipient
  - Double blue check (`✓✓` in `#53bdeb`): Read by recipient
- **Real-Time Indicators**:
  - Live **"typing..."** status indicator in chat header.
  - Live **Online** presence indicator dot.
  - Disappearing timer badge on every bubble (e.g. `⏱️ 24h left`).
- **Media & Extras**:
  - Client-side canvas photo compression (max 1280px JPEG).
  - Video validation (<= 2 minutes, <= 50MB).
  - Lightbox image viewer.
  - Quick emoji drawer (😀, 😂, ❤️, 👍, 🔥, 🎉, 🙏, 👏, 🚀, ⏳).
  - In-app Contact Directory Search (`/api/users/search`).

### 4. ⏳ 24-Hour Self-Destruction Engine
- Every message automatically expires 24 hours (`86,400,000 ms`) after creation.
- A background worker runs every 30 seconds:
  - Deletes expired message records from SQLite.
  - Unlinks expired photos/videos from `uploads/`.
  - Dispatches real-time `vanish` event to connected clients to animate and remove bubbles instantly without refreshing.

---

## ⚡ Quick Start

```bash
cd /Users/chandradipbanerjee/Documents/Projects_GenAI/vanish
npm start
```
Open **`http://localhost:3000`** in your browser.

---

## 🧪 Testing

```bash
npm test
```
All 12 automated test cases pass cleanly verifying validation, database persistence, read receipts, and TTL pruning.
