# ECHO — AI-Powered Customer Support Platform

> **ECHO** is a multi-tenant B2B SaaS platform that uses Retrieval-Augmented Generation (RAG) to instantly answer customer questions from your own documentation — with voice, chat, and email support built in.

---

## Overview

ECHO lets businesses embed an intelligent support assistant trained on their own documents. Upload your PDFs, DOCX, or Markdown files, connect your preferred AI provider, and deploy a fully functional support system — complete with a chat widget, voice calls, email processing, human handoff, and a unified ticket dashboard.

Built as a college capstone project using Next.js, MongoDB, and shadcn/ui.

---

## Features

- **Document-based RAG** — Upload PDFs, DOCX, TXT, or Markdown files. ECHO chunks and indexes them using a custom TF-IDF engine with zero retrieval cost.
- **Bring Your Own Key (BYOK)** — Each tenant configures their own AI provider key. Supports Groq (free), OpenAI, Claude, Gemini, and OpenRouter.
- **Multi-channel support** — Chat widget, full voice calls (browser Speech API), and inbound email processing in one platform.
- **Human handoff** — Configurable retry threshold. After N unsatisfactory AI responses, conversations automatically escalate to human agents.
- **Unified ticket system** — Every interaction (chat, voice, email) generates a ticket with a full conversation view for agents.
- **Analytics dashboard** — Conversation trends, channel breakdown, AI resolution rate, confidence metrics, and top document sources.
- **Super Admin panel** — Platform-wide monitoring of all organizations, users, and activity.
- **Embeddable widget** — One-line embed code to add ECHO to any website.
- **Flexible email ingestion** — SendGrid/Mailgun webhook, IMAP polling, or manual forwarding.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14 (App Router) + shadcn/ui + Tailwind CSS |
| Database | MongoDB (Mongoose ODM) |
| Auth | NextAuth.js (Credentials + JWT) |
| RAG Engine | Custom TF-IDF + cosine similarity (zero API cost) |
| AI Providers | Groq, OpenAI, Claude, Gemini, OpenRouter |
| Voice | Web Speech API (browser-native STT + TTS) |
| File Parsing | pdf-parse + mammoth |

---

## Getting Started

### Prerequisites

- Node.js 18+
- MongoDB (local instance or a free [MongoDB Atlas](https://www.mongodb.com/atlas) cluster)

### Installation

**1. Clone the repository and install dependencies:**

```bash
git clone https://github.com/your-username/echo.git
cd echo
npm install
```

**2. Configure environment variables:**

```bash
cp .env.example .env.local
```

Open `.env.local` and set the following:

| Variable | Description |
|---|---|
| `MONGODB_URI` | Your MongoDB connection string |
| `NEXTAUTH_SECRET` | Generate with: `openssl rand -base64 32` |

**3. Start the development server:**

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) and register your account.

---

## Quick Start Guide

1. **Register** — Creating an account also creates your organization automatically.
2. **Upload documents** — Go to **Documents**, drag and drop your support PDFs, DOCX, or TXT files.
3. **Configure AI** — Go to **Settings → AI Provider**, select Groq (free tier available), and paste your API key.
4. **Test the widget** — Go to **Settings → Embed Code**, copy your Org ID, then visit `/widget?orgId=YOUR_ORG_ID`.
5. **Set up email** — Go to **Settings → Email** and configure your preferred ingestion method (webhook, IMAP, or forwarding).
6. **Monitor** — Use **Analytics** for metrics and **Tickets** to manage escalations.

---

## Super Admin Access

To access the platform-wide admin panel, update a user's role directly in MongoDB:

```js
db.users.updateOne(
  { email: "your@email.com" },
  { $set: { role: "super_admin" } }
)
```

Then navigate to `/admin` from the sidebar.

---

## Project Structure

```
app/
├── api/
│   ├── auth/           # NextAuth + registration
│   ├── chat/           # RAG + AI chat endpoint + feedback
│   ├── documents/      # Upload, process, delete documents
│   ├── email/          # Inbound webhook, IMAP poll, manual forward
│   ├── tickets/        # Ticket CRUD + agent replies
│   ├── conversations/  # Conversation history
│   ├── settings/       # Org settings (BYOK, email, widget)
│   ├── analytics/      # Platform analytics
│   ├── admin/          # Super admin data
│   └── widget/         # Public widget config
├── auth/               # Login & register pages
├── dashboard/
│   ├── analytics/      # Charts and metrics
│   ├── conversations/  # Conversation viewer
│   ├── documents/      # Document management
│   ├── settings/       # BYOK + email + widget config
│   └── tickets/        # Unified ticket view with chat window
├── admin/              # Super admin platform panel
└── widget/             # Embeddable chat + voice widget

lib/
├── ai-providers.ts     # Unified AI layer (Groq, OpenAI, Claude, Gemini, OpenRouter)
├── auth.ts             # NextAuth configuration
├── db.ts               # MongoDB connection
├── email-processor.ts  # Email → RAG → AI pipeline
└── rag.ts              # TF-IDF engine (chunking, vectorization, search)

models/                 # Mongoose schemas
```

---

## RAG + AI Pipeline

```
Upload    →  Text extracted from PDF / DOCX / TXT
Chunk     →  Split into ~500-character overlapping chunks
Index     →  TF-IDF vectors computed per chunk
Query     →  User question vectorized; top-5 chunks retrieved via cosine similarity
Generate  →  Retrieved chunks passed as context to the configured LLM
Feedback  →  User rates response; retry count tracks toward handoff threshold
Escalate  →  After N failures, a ticket is created and the agent is notified
```

---

## Cost

The RAG retrieval layer is entirely free. AI generation cost depends on the tenant's chosen provider:

| Provider | Free Tier | Notes |
|---|---|---|
| Groq | ✅ Yes (generous) | Llama 3.1, Mixtral — fast and free |
| Gemini | ✅ Yes (limited) | Gemini 1.5 Flash |
| OpenRouter | ✅ Some models | Meta Llama free tier available |
| OpenAI | ❌ No | Pay-per-token |
| Claude | ❌ No | Pay-per-token |

MongoDB Atlas free tier handles typical college project loads with ease.

---

## License

[MIT](LICENSE) — Built as a college capstone project.
