<div align="center">

# 🤖 Cracker AI

### Real-Time AI Interview Assistant

*Ace every interview with live AI coaching powered by voice, RAG, and your own documents*

[![Node.js](https://img.shields.io/badge/Node.js-v20%2B-339933?logo=node.js&logoColor=white)](https://nodejs.org)
[![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.6-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Firebase](https://img.shields.io/badge/Firebase-Firestore-FF6F00?logo=firebase&logoColor=white)](https://firebase.google.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

<br/>

![Cracker AI Preview](screenshots/preview.png)

</div>

---

## <div align="center">📖 Overview</div>

Cracker AI is a production-grade SaaS application that provides real-time AI coaching during live job interviews. It combines **streaming speech-to-text**, **large language models**, and **Retrieval-Augmented Generation (RAG)** to instantly surface relevant answers drawn from the user's own resume and documents — all while the interview is happening.

The core is built around a **custom Node.js WebSocket server** that acts as a secure relay between the browser and Deepgram's STT API, enabling sub-second transcription without exposing API credentials client-side. An Electron desktop app provides an always-on-top transparent overlay widget for a seamless, invisible-to-interviewer experience.

---

## <div align="center">🏗️ Architecture Overview</div>

The system is designed with a strict, unidirectional layered architecture. Communication between layers is decoupled using WebSockets and Firestore, ensuring fault-isolation, modularity, and high testability.

```mermaid
flowchart TD
    A([🌐 Next.js Web App\nReact / Browser]) -->|WebSocket upgrade| C
    B([🖥️ Electron Desktop\nTransparent Overlay]) -->|localhost relay| C

    subgraph SERVER ["⚙️ Server Layer — server.js (Node.js HTTP + WSS)"]
        C[WebSocket Server\nDeepgram Relay]
        D[Next.js Request Handler\nApp Router]
    end

    C -->|Streams audio| E([🎙️ Deepgram API\nStreaming STT])
    E -->|Transcript chunks| C
    C --> D

    D --> F[/api/ai\nSSE Answer Stream]
    D --> G[/api/documents\nRAG + Embeddings]
    D --> H[/api/billing\nRazorpay Webhooks]
    D --> I[/api/auth\nClerk + JWT]

    F -->|Prompt + context| J([🧠 OpenAI GPT-4o\nAnswer Generation])
    G -->|Embed chunks| K([🔢 OpenAI Embeddings\ntext-embedding-3-small])

    subgraph DB ["🗄️ Data Layer — Firebase Firestore"]
        L[(users\nprofiles)]
        M[(sessions\nhistory)]
        N[(documents\nvector embeddings)]
        O[(usage\ncredits)]
    end

    G <-->|findNearest\nvector search| N
    F <-->|read context| M
    I <-->|auth & roles| L
    H <-->|credit metering| O

    style SERVER fill:#1a1a2e,stroke:#4e4e8a,color:#fff
    style DB fill:#1a2e1a,stroke:#4e8a4e,color:#fff
```

### <div align="center">Key Design Decisions</div>

| Decision | Rationale |
|---|---|
| Custom `server.js` over `next start` | WebSocket upgrade requires raw `http.Server` access — not possible with Next.js standalone |
| Firestore Vector Search over Pinecone/Weaviate | Zero extra infrastructure; native `findNearest` on existing collection reduces ops complexity |
| Provider-agnostic AI layer (`providers.ts`) | Swap LLM providers (OpenAI ↔ Groq) without touching business logic |
| Clerk + Firebase Auth dual-layer | Clerk handles OAuth UX; Firebase session cookies enable WebSocket authentication |
| Electron over browser extension | No review cycles or content policy; ships immediately with full system-level access |

---

## <div align="center">✨ Features</div>

### Core
- 🎙️ **Real-Time Speech-to-Text** — Persistent WebSocket relay to Deepgram with `interim_results` for sub-second transcription and automatic silence detection
- 🧠 **AI Answer Generation** — Streams GPT-4o responses via Server-Sent Events (SSE); provider-agnostic (swap OpenAI / Groq at runtime)
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

## <div align="center">🛠️ Tech Stack</div>

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

## <div align="center">🚀 Getting Started</div>

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

---

## <div align="center">🖥️ Desktop App (Electron)</div>

The Electron app renders the web app in a transparent, frameless, always-on-top window — perfect for use during screen-share interviews without detection.

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

## <div align="center">🧪 Testing</div>

```bash
npm test
```

Uses Node.js built-in test runner (`node:test`). Test files live in `tests/` and follow the `*.test.js` naming convention.

---

## <div align="center">📦 Deployment</div>

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

## <div align="center">🔧 Troubleshooting</div>

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

## <div align="center">🤝 Contributing</div>

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit with conventional commits: `git commit -m "feat: add your feature"`
4. Push and open a Pull Request against `main`

Please ensure `npm run lint` passes before submitting a PR.

---

## <div align="center">📄 License</div>

This project is licensed under the **MIT License**.

---

<div align="center">
Built with ❤️ by <a href="https://github.com/Nityanand-7484">Nityanand Kumar</a>
</div>