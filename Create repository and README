# BoloBill[README.md](https://github.com/user-attachments/files/26132549/README.md)
# 🍽️ BoloBill — Voice-Based Restaurant Billing System

> **Atmiya University Hackathon Project**  
> Shree Krishna Dhaba, Rajkot, Gujarat

---

## 🚀 Live Demo

**Deployed on Netlify:** `https://bolobill-rushreliefer.netlify.app`  
Scan any table QR → Customer self-order menu opens automatically

---

## 📌 What is BoloBill?

BoloBill is a **voice-powered smart restaurant billing system**. Waiters speak the order in English, Hindi, or Gujarati — the system understands it and generates a GST-compliant tax invoice in under 30 seconds. Customers can also self-order by scanning a QR code on their table.

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 🎙️ Voice Ordering | Waiter speaks — AI parses order instantly |
| 🤖 Gemini AI | Understands fuzzy speech, Hindi/Gujarati names |
| 📱 Customer Self-Order | Scan QR → browse menu → order from phone |
| 🔔 Real-Time Notifications | Waiter gets instant alert for new orders and bill requests |
| 💰 Smart Payment Flow | Customer calls waiter → waiter collects UPI/Cash → marks done |
| 🧾 GST Invoice | Full tax invoice with GSTIN, HSN codes, CGST+SGST |
| 📡 Cross-Device Sync | Firebase Realtime Database — works on all devices |
| 🔐 Role-Based Login | Manager and Waiter PIN login with lockout protection |
| 📊 Order History | Every bill saved with CSV export |
| 🖨️ Print Bill | Professional A4 GST invoice with restaurant branding |
| 💬 WhatsApp Share | Send bill directly to customer WhatsApp |
| 🍳 Kitchen Display | KDS window for kitchen staff |

---

## 🛠️ Tech Stack

### Frontend
- **HTML5 + CSS3 + Vanilla JavaScript** — Single file app, zero dependencies
- **Web Speech API** — Browser-native voice recognition (Chrome)
- **QRCode.js** — Real scannable UPI and table QR generation
- **GSAP 3** — Smooth background food icon animations

### Backend / APIs
- **Firebase Realtime Database** — Cross-device order sync via REST API
- **Google Gemini 2.5 Flash** — AI-powered voice order parsing
- **Web Speech Synthesis API** — Reads bill total aloud

### Hosting
- **Netlify** — Frontend static hosting (free tier)
- **Firebase** — Backend real-time database (free tier)

---

## 📁 Project Structure

```
BoloBill/
├── index.html        ← Complete frontend (single HTML file — open in browser)
├── backend/
│   └── server.js     ← Node.js + Express backend (optional V2 upgrade)
└── README.md         ← Documentation
```

---

## ⚙️ Setup & Run

### Option 1 — Just open in browser (Quickest)
```
1. Download index.html
2. Open in Google Chrome
3. Works immediately with local storage
```

### Option 2 — With Firebase (Cross-device sync)
```
1. Go to console.firebase.google.com
2. Create project → Realtime Database → Start in Test Mode
3. Copy your Database URL (e.g. https://your-project-rtdb.firebaseio.com)
4. In app: Manager Panel → Cross-Device Sync → paste URL → Save
```

### Option 3 — With Gemini AI (Smartest voice parsing)
```
1. Go to aistudio.google.com → Get API Key (free)
2. In app: Manager Panel → AI Settings → paste key → Save
3. Voice orders now use AI — understands Hindi and Gujarati too!
```

---

## 🎯 Complete Demo Flow

```
1. Manager sets restaurant profile + menu + UPI ID
2. Manager generates Table QR codes
3. Customer scans QR → sees welcome screen with restaurant branding
4. Customer enters name + WhatsApp → browses menu → orders by voice or tap
5. Waiter gets RED notification on laptop → accepts → bill loads automatically
6. Customer eats food
7. Customer taps "Done Eating" → Waiter gets ORANGE notification with amount
8. Waiter goes to table → shows UPI QR on phone → customer pays
9. Waiter clicks "Mark as Paid" → bill saved to history automatically
10. Customer phone shows "Thank You!" screen
```

---

## 🔑 Default Login Credentials

| Role | Name | PIN |
|---|---|---|
| Manager | Admin | 1234 |
| Waiter | Ramesh Patel | 2345 |
| Waiter | Suresh Kumar | 3456 |
| Waiter | Priya Shah | 4567 |

---

## 🌐 API Reference

### Firebase Realtime Database (REST)

| Method | Endpoint | Purpose |
|---|---|---|
| PUT | /orders.json | Save all pending orders |
| GET | /orders.json | Fetch all orders |
| PUT | /profile.json | Save restaurant profile and UPI ID |
| GET | /profile.json | Fetch profile on customer phone |

### Google Gemini AI

| Field | Value |
|---|---|
| Model | gemini-2.5-flash |
| Endpoint | https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent |
| Auth Header | x-goog-api-key |

---

## 🏗️ V2 Production Architecture

```
Customer Phone  →  React Native App
Waiter Tablet   →  Next.js Dashboard
                        ↓
               Node.js + Express API
                        ↓
            PostgreSQL + Redis Cache
                        ↓
               WebSocket (Socket.io)
               for real-time updates
```

---

## 👨‍💻 Built By

Student Team — SyntaxError
Rajkot, Gujarat, India

---

## 📄 License

MIT License — Free to use and modify
