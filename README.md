# 🤖 Cracker AI

> **AI-powered real-time interview assistant** — get instant coaching and answers during live interviews using voice, AI, and your own documents.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Node](https://img.shields.io/badge/node-%3E%3D20-brightgreen)
![Next.js](https://img.shields.io/badge/Next.js-15-black)
![Firebase](https://img.shields.io/badge/Firebase-Firestore-orange)

---

## ✨ Features

- 🎙️ **Live Speech Transcription** — Sub-second streaming speech-to-text via WebSockets
- 🧠 **AI Answer Generation** — Real-time LLM-powered answers to interview questions
- 📄 **RAG Architecture** — Searches your uploaded resumes/docs to personalize AI answers
- 💳 **SaaS Billing** — Free / Pro / Team subscription tiers
- 🖥️ **Desktop Overlay** — Electron app with a transparent, always-on-top widget
- 🛡️ **Admin Dashboard** — Internal tooling for system health and usage monitoring

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Next.js 15, React, Tailwind CSS |
| **Backend** | Node.js, Custom WebSocket Server |
| **Database** | Firebase Firestore (with Vector Search) |
| **Auth** | Clerk / Firebase Auth |
| **AI / STT** | OpenAI GPT-4o, Deepgram |
| **Payments** | Razorpay |
| **Desktop** | Electron |

---

## 🚀 Getting Started

### Prerequisites

- Node.js **v20+**
- A Firebase project (Firestore + Auth enabled)
- API keys for: OpenAI, Deepgram, Razorpay, Clerk

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/Nityanand-7484/cracker-ai.git
cd cracker-ai

# 2. Install dependencies
npm install

# 3. Set up environment variables
cp .env.example .env
# Fill in your credentials in .env

# 4. Start the development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## ⚙️ Environment Variables

Copy `.env.example` and fill in your own values. **Never commit your `.env` file.**

Key variables required:

| Variable | Description |
|---|---|
| `NEXT_PUBLIC_FIREBASE_*` | Firebase client config |
| `FIREBASE_PRIVATE_KEY` | Firebase Admin SDK private key |
| `OPENAI_API_KEY` | OpenAI API key |
| `DEEPGRAM_API_KEY` | Deepgram speech-to-text key |
| `CLERK_SECRET_KEY` | Clerk authentication key |
| `RAZORPAY_KEY_SECRET` | Razorpay payment secret |
| `NEXTAUTH_SECRET` | NextAuth session secret |
| `DATABASE_URL` | Database connection string |

See `.env.example` for the full list of required variables.

---

## 🖥️ Desktop App (Electron)

Run the transparent overlay widget:

```bash
# Make sure the web app is running on port 3000 first, then:
cd desktop
npm install
npm run start
```

---

## 📦 Deployment

Cracker AI uses WebSockets and requires a **persistent Node.js server**. Recommended platforms:

- **Render** (Web Service)
- **Railway**
- **Heroku**
- Any VPS (DigitalOcean, AWS EC2)

> ⚠️ Standard serverless platforms (like Vercel) will not support WebSocket connections out of the box.

```bash
# Build command
npm install && npm run build

# Start command
npm run start
```

Add all environment variables securely through your platform's dashboard — **never hardcode secrets**.

---

## 🤝 Contributing

Contributions are welcome! Please open an issue or submit a pull request.

---

## 📄 License

This project is licensed under the MIT License.