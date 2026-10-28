# WhatsApp Vanish — 24-Hour Ephemeral Private Messenger

A full-stack, production-grade messaging web application designed like **WhatsApp Web** with **24-hour self-destructing messages, photos, and videos**.

Built with **Native Node.js 24 + SQLite (`node:sqlite`) + Real-time Server-Sent Events (SSE)**. Zero external npm dependencies.

---

## 🚀 Key Features

### 1. 📱 Proper Phone Number Validation (E.164 Strict)
- Country code selector dropdown with country flags (🇮🇳 +91, 🇺🇸 +1, 🇬🇧 +44, 🇦🇺 +61, 🇩🇪 +49, 🇦🇪 +971, 🇸🇬 +65, 🇫🇷 +33, 🇯🇵 +81, or Custom country code).
- Strict length and prefix validation according to international telecommunication standards (e.g. 10 digits for India/USA, 10-11 for UK).
- Simulated 6-digit SMS OTP verification (standard test code: `123456`).

### 2. 👤 User Registration & Profile Storage in Database
- **Step 1**: Phone number verification.
- **Step 2**: Enter 6-digit SMS OTP code.
- **Step 3 (Profile Setup)**:
  - **Full Name** (Required, stored in SQLite `users` table).
  - **About / Status** (e.g. *"Available"*, *"Busy"*, *"In a meeting"*).
  - **Avatar Color** picker with initials generator.
- All profile details are persisted in SQLite `vanish.db` and rendered on contacts, headers, and search results.

### 3. 💬 WhatsApp Web UI & Real-Time UX
- **Authentic WhatsApp Theme**:
  - Signature WhatsApp teal header (`#008069`), green accents (`#00a884`), and subtle doodle chat wallpaper.
  - Outgoing message bubble (`#D9FDD3`) and incoming message bubble (`#FFFFFF`).
  - Read receipts with checkmarks:
    - Single gray check (`✓`): Sent
    - Double gray check (`✓✓`): Delivered
    - Double blue check (`✓✓` in `#53bdeb`): Read by recipient
- **Real-Time Indicators**:
  - Live **"typing..."** status indicator in chat header.
  - Live **Online** presence indicator dot.
  - Disappearing timer badge on every bubble (e.g. `⏱️ 24h left`).
- **Media & Extras**:
  - Instant client-side canvas photo compression (max 1280px JPEG).
  - Video validation (<= 2 minutes, <= 50MB).
  - Lightbox image viewer.
  - Quick emoji drawer (😀, 😂, ❤️, 👍, 🔥, 🎉, 🙏, 👏, 🚀, ⏳).
  - Contact directory search (`/api/users/search`).

### 4. ⏳ 24-Hour Self-Destruction Engine
- Every message automatically expires 24 hours (`86,400,000 ms`) after creation.
- A background worker runs every 30 seconds:
  - Deletes expired message records from SQLite.
  - Unlinks expired photos/videos from `uploads/`.
  - Dispatches real-time `vanish` event to connected clients to animate and remove bubbles instantly without refreshing.

---

## 📁 Database Schema (`vanish.db`)

```sql
-- Users & Profiles
CREATE TABLE users (
  phone TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  about TEXT DEFAULT 'Hey there! I am using WhatsApp Vanish',
  avatar_color TEXT DEFAULT '#00a884',
  avatar_url TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  last_seen INTEGER NOT NULL
);

-- Disappearing Messages
CREATE TABLE messages (
  id TEXT PRIMARY KEY,
  chat_id TEXT NOT NULL,
  sender_phone TEXT NOT NULL,
  receiver_phone TEXT NOT NULL,
  members TEXT NOT NULL,
  text TEXT,
  media_url TEXT,
  media_type TEXT,
  status TEXT DEFAULT 'sent', -- 'sent', 'delivered', 'read'
  created_at INTEGER NOT NULL,
  expire_at INTEGER NOT NULL
);

CREATE INDEX idx_messages_chat_id ON messages(chat_id);
CREATE INDEX idx_messages_expire_at ON messages(expire_at);
CREATE INDEX idx_messages_status ON messages(status);
```

---

## ⚡ Quick Start

### 1. Launch the Server
```bash
cd /Users/chandradipbanerjee/Documents/Projects_GenAI/vanish
npm start
```
Open **`http://localhost:3000`** in your browser.

### 2. Simulate Chatting Between Two Users
1. **Window 1 (User 1)**:
   - Select 🇮🇳 **India (+91)** $\rightarrow$ Enter `98765 43210` $\rightarrow$ Click **Next / Send Code**
   - Enter OTP `123456` $\rightarrow$ Click **Verify Code**
   - Fill in Profile: Name = `Chandradip Banerjee`, Status = `Available` $\rightarrow$ **Complete & Start Chatting**
2. **Window 2 / Incognito (User 2)**:
   - Select 🇺🇸 **USA (+1)** $\rightarrow$ Enter `555 0100 22` $\rightarrow$ Click **Next / Send Code**
   - Enter OTP `123456` $\rightarrow$ Click **Verify Code**
   - Fill in Profile: Name = `Sarah Connor` $\rightarrow$ **Complete & Start Chatting**
3. **Start Chatting**:
   - In Window 1, click the **💬 (New Chat)** icon.
   - Search `Sarah` or type `+1555010022` $\rightarrow$ Click **Start Chat**.
   - Type a message $\rightarrow$ Notice the real-time **typing...** indicator in Window 2!
   - Send the message $\rightarrow$ See the single check (`✓`), double check (`✓✓`), and double blue check (`✓✓`) once viewed!

### 3. Run Automated Tests
```bash
npm test
```
All 12 production test cases run in-process and verify validation, DB persistence, read receipts, and TTL pruning.
