<div align="center">

# 🤖 Cracker AI

### Real-Time AI Interview Assistant

*Ace every interview with live AI coaching powered by voice, RAG, and your own documents*

[![Node.js](https://img.shields.io/badge/Node.js-v20%2B-339933?logo=node.js&logoColor=white)](https://nodejs.org)
[![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.6-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Firebase](https://img.shields.io/badge/Firebase-Firestore-FF6F00?logo=firebase&logoColor=white)](https://firebase.google.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

---

## 📖 Overview

Cracker AI is a production-grade SaaS application that provides real-time AI coaching during live job interviews. It combines **streaming speech-to-text**, **large language models**, and **Retrieval-Augmented Generation (RAG)** to instantly surface relevant answers drawn from the user's own resume and documents — all while the interview is happening.

The architecture is built around a **custom Node.js WebSocket server** that acts as a secure relay between the browser and Deepgram's STT API, enabling sub-second transcription without exposing API credentials client-side. An Electron desktop app provides an always-on-top transparent overlay widget for a seamless, invisible-to-interviewer experience.

---

## ✨ Features

### Core
- 🎙️ **Real-Time Speech-to-Text** — Persistent WebSocket relay to Deepgram; `interim_results` for sub-second transcription with automatic silence detection
- 🧠 **AI Answer Generation** — Streams GPT-4o responses via Server-Sent Events (SSE); provider-agnostic via `src/lib/ai/providers.ts` (swap OpenAI / Groq / any OpenAI-compatible endpoint)
- 📄 **RAG over Personal Documents** — Uploads parsed via `pdf-parse` / `mammoth`, chunked, embedded via OpenAI `text-embedding-3-small`, stored in Firestore with native `findNearest` vector search
- 🎭 **Mock Interview Mode** — Full AI-driven mock interviewer with question generation, follow-up logic, and session scoring
- 🌐 **Real-Time Translation** — On-the-fly question/answer translation via `/api/translate`

### Platform
- 💳 **SaaS Billing** — Razorpay-integrated Free / Pro / Team tiers with usage metering and webhook lifecycle management
- 🛡️ **Admin Dashboard** — Internal route (`/admin`) with system health monitoring, user management, and usage analytics
- 🖥️ **Electron Desktop App** — Transparent, always-on-top frameless window; communicates with the web app over localhost
- 📊 **Usage Tracking** — Per-user session credits tracked in Firestore with rate limiting
- 🔐 **Auth** — Clerk-first authentication with Firebase Auth fallback; JWT verification in middleware

---

## 🛠️ Tech Stack

| Layer | Technology | Version |
|---|---|---|
| **Framework** | Next.js (App Router) | 14.x |
| **Language** | TypeScript | 5.6 |
| **Styling** | Tailwind CSS + Framer Motion | 3.x / 14.x |
| **Auth** | Clerk + Firebase Auth | 5.x |
| **Database** | Firebase Firestore | 12.x |
| **AI / LLM** | OpenAI GPT-4o (+ Groq fallback) | openai 7.x |
| **STT** | Deepgram Streaming WebSocket | @deepgram/sdk 5.x |
| **Embeddings** | OpenAI `text-embedding-3-small` | — |
| **Payments** | Razorpay | 2.x |
| **Desktop** | Electron | — |
| **WebSocket** | `ws` library | 8.x |
| **Validation** | Zod | 3.x |
| **Document Parsing** | `pdf-parse`, `mammoth` (DOCX) | — |

---

## 📁 Project Structure

```
cracker-ai/
├── server.js                  # Custom HTTP + WebSocket server (entry point)
├── src/
│   ├── app/                   # Next.js App Router
│   │   ├── (auth)/            # Auth layout group (Clerk)
│   │   ├── admin/             # Admin dashboard (role-gated)
│   │   ├── api/               # API route handlers
│   │   │   ├── ai/            # Answer generation (SSE streaming)
│   │   │   ├── billing/       # Razorpay checkout + webhooks
│   │   │   ├── documents/     # Upload, embed, retrieve
│   │   │   ├── mock-interviews/ # Mock session management
│   │   │   ├── sessions/      # Live session CRUD
│   │   │   ├── transcription/ # STT fallback (REST)
│   │   │   ├── translate/     # Real-time translation
│   │   │   └── usage/         # Credit metering
│   │   ├── dashboard/         # Main user dashboard
│   │   ├── billing/           # Subscription UI
│   │   ├── documents/         # Document manager UI
│   │   ├── mock-interviews/   # Mock interview UI
│   │   └── sessions/          # Live session UI
│   ├── components/
│   │   ├── live/              # Real-time session components
│   │   ├── dashboard/         # Dashboard widgets
│   │   ├── ui/                # Shared design system (Button, Card, etc.)
│   │   └── layout/            # Nav, Sidebar, Shell
│   ├── lib/
│   │   ├── ai/
│   │   │   ├── providers.ts   # LLM provider abstraction
│   │   │   ├── answer-generator.ts # RAG + prompt pipeline
│   │   │   ├── embeddings.ts  # Vector embedding utils
│   │   │   ├── retriever.ts   # Firestore vector search
│   │   │   ├── mock-interviewer.ts # Mock interview AI logic
│   │   │   └── resume-parser.ts    # Resume ingestion
│   │   ├── firebase/          # Firebase client SDK init
│   │   ├── firebase-admin.ts  # Firebase Admin SDK (server-only)
│   │   ├── rate-limit.ts      # Token bucket rate limiter
│   │   ├── razorpay.ts        # Payment helpers
│   │   └── usage.ts           # Credit deduction logic
│   ├── hooks/                 # Custom React hooks
│   ├── types/                 # Global TypeScript types
│   └── middleware.ts          # Clerk auth + route protection
├── desktop/                   # Electron app
├── tests/                     # Node test runner tests
├── scripts/                   # Utility/migration scripts
└── adapters/                  # External service adapters
```

---

## 🚀 Getting Started

### Prerequisites

| Requirement | Version | Notes |
|---|---|---|
| Node.js | ≥ 20 | LTS recommended |
| npm | ≥ 10 | Bundled with Node 20 |
| Firebase Project | — | Firestore + Auth enabled |
| OpenAI Account | — | GPT-4o + Embeddings access |
| Deepgram Account | — | Streaming STT (12k min/month free tier) |
| Clerk Account | — | Authentication provider |
| Razorpay Account | — | For billing (test mode works) |

### 1. Clone & Install

```bash
git clone https://github.com/Nityanand-7484/cracker-ai.git
cd cracker-ai
npm install
```

### 2. Configure Environment

```bash
cp .env.example .env
# Fill in your credentials in .env
```

See `.env.example` for the full list of required variables.

### 3. Firebase Setup

1. Create a project at [console.firebase.google.com](https://console.firebase.google.com)
2. Enable **Firestore Database** (Native mode)
3. Enable **Authentication** → Email/Password + Google
4. Go to **Project Settings → Service Accounts → Generate new private key**
5. Paste the values into your `.env` file

> **Vector Search Note:** The `documents` collection uses Firestore's native `findNearest` for vector search. Your Firebase project must be on the **Blaze (pay-as-you-go) plan** for this feature.

### 4. Razorpay Setup

1. Create an account at [dashboard.razorpay.com](https://dashboard.razorpay.com)
2. Go to **Settings → API Keys** → Generate Test Key
3. Create two Plans (monthly recurring): **Pro** and **Team**
4. Copy Plan IDs into your `.env`
5. Set up a Webhook pointing to `https://your-domain.com/api/billing/webhook`
   - Events: `payment.captured`, `subscription.cancelled`, `subscription.completed`

### 5. Run Locally

```bash
npm run dev
```

> ⚠️ **Do not use `next dev`** — the dev server must be started via `node server.js` (aliased to `npm run dev`) to initialize the WebSocket server for Deepgram relay.

Open [http://localhost:3000](http://localhost:3000).

---

## 🖥️ Desktop App (Electron)

The Electron app renders the web app in a transparent, frameless, always-on-top window for use during screen-share interviews.

```bash
# Terminal 1 — Web server must be running first
npm run dev

# Terminal 2 — Launch Electron overlay
npm run desktop
```

**Build for distribution:**

```bash
npm run desktop:build   # Builds for Windows (.exe installer)
```

---

## 🧪 Testing

```bash
npm test
```

Uses Node.js built-in test runner (`node:test`). Test files live in `tests/` and follow the `*.test.js` naming convention.

---

## 📦 Deployment

Cracker AI **requires a persistent Node.js process** due to WebSocket connections. Standard serverless deployments will terminate WebSocket connections.

### Recommended: Render / Railway

1. Push your code to GitHub (ensure `.env` is in `.gitignore`)
2. Create a new **Web Service**
3. Set **Build Command:** `npm install && npm run build`
4. Set **Start Command:** `npm run start`
5. Add all environment variables via the platform dashboard
6. Enable **Auto-Deploy** on push to `main`

### Self-Hosted (Docker)

A `docker-compose.yml` is included for self-hosted deployments:

```bash
docker-compose up -d
```

> ⚠️ Ensure your hosting provider supports **persistent WebSocket connections** and does not enforce a 60-second request timeout.

---

## 🔧 Troubleshooting

**Admin dashboard shows 403**
Send `POST /api/admin/init`, or manually set `role: "ADMIN"` on your `profiles` document in Firestore.

**WebSocket disconnects immediately**
- Confirm your Deepgram API key is correctly set in `.env`
- Set `NEXT_PUBLIC_SPEECH_PROVIDER=mock` to run without a Deepgram key during development

**Firebase private key formatting error**
- Wrap the key in double quotes in `.env`
- Keep `\n` as literal characters — do not use actual newlines

**`TypeError` on `getApps()` in server.js**
Ensure `firebase-admin` v12+ is installed — the server uses modular imports, not legacy `admin.apps` syntax.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit with conventional commits: `git commit -m "feat: add your feature"`
4. Push and open a Pull Request against `main`

Please ensure `npm run lint` passes before submitting a PR.

---

## 📄 License

This project is licensed under the **MIT License**.

---

<div align="center">
Built with ❤️ by <a href="https://github.com/Nityanand-7484">Nityanand Kumar</a>
</div>