# OperatorApp


AI-augmented customer support for e-commerce — live chat that translates between operator and customer in real time, surfaces context (cart, orders, page history, sentiment) as it becomes relevant, and lets operators answer from a private knowledge base with one click.

> 📹 **Demo videos:** the [`LINKTOVIDEOS.txt`](./LINKTOVIDEOS.txt) file links to a Drive folder showing the project end-to-end. **Start with the intro video.**

---

## What's in this organization

| Repository | What it is | Stack |
| --- | --- | --- |
| [`backend`](https://github.com/OperatorApp/backend) | REST + WebSocket API, AI services, persistence | Node.js · Express · Prisma · PostgreSQL · Socket.IO · OpenAI |
| [`frontend`](https://github.com/OperatorApp/frontend) | Operator dashboard (the support agent's UI) | React 19 · Vite · React Router · Socket.IO client |
| [`chat-component-frontend`](https://github.com/OperatorApp/chat-component-frontend) | Embeddable customer-side chat widget for the storefront | React 19 · Vite · Socket.IO client |
| `README` | This repo — project overview and demo links | — |

---

## What it does

- **Real-time chat** between customers (on a storefront) and operators (in the dashboard), powered by Socket.IO.
- **Automatic translation** of every message — original text and translated text are stored side-by-side, with detected language.
- **Per-operator knowledge base** synced to an OpenAI vector store; operators upload their own product/policy info and the AI answers from it (no hallucinated product names or links).
- **Prompt buttons** — operators save reusable prompts (e.g. *"draft a refund-status reply"*) and fire them against the knowledge base in one click; the response is sent into the thread.
- **Paint State** — a relevance-scoring engine that watches each conversation and rates how much it's about each of six context sections (customer / session / URL trail / cart / orders / sentiment). Each thread gets its own hue (golden-angle spread, so adjacent threads look distinct), and section panels brighten as their relevance score climbs. This gives operators an at-a-glance visual map of "what is this conversation actually about."
- **Customer simulation** — a sandbox endpoint that spins up an AI customer for testing operator flows.
- **Two auth modes** — JWT for operators logging into the dashboard, hashed API keys for the embedded widget on the storefront.

---

## Quick start

The three runnable repos each have their own README with setup instructions. The recommended order to bring the system up locally:

1. **[`backend`](https://github.com/OperatorApp/backend)** — install dependencies, set up PostgreSQL, run Prisma migrations, start the server (defaults to port `3001`).
2. **[`frontend`](https://github.com/OperatorApp/frontend)** — operator dashboard, runs on Vite's dev server (default `5173`) and proxies `/api` to the backend.
3. **[`chat-component-frontend`](https://github.com/OperatorApp/chat-component-frontend)** — the customer widget, also a Vite dev server. Connects to the backend Socket.IO endpoint.

You'll need:
- Node.js 18+
- PostgreSQL 14+
- An OpenAI API key (for translation, knowledge-base queries, and paint-state scoring)

---

## Architecture at a glance

```
┌─────────────────────────────┐         ┌──────────────────────────────┐
│  chat-component-frontend    │         │  frontend (operator UI)      │
│  (embedded on storefront)   │         │  React + Vite                │
│  React + Vite               │         │  ─ thread list + filters     │
│  ─ chat bubble UI           │         │  ─ context panel (paint)     │
│  ─ Socket.IO client         │         │  ─ knowledge base + prompts  │
└──────────────┬──────────────┘         └──────────────┬───────────────┘
               │                                        │
               │   Socket.IO + REST (/auth, /thread,    │
               │   /ai, /operator)                      │
               ▼                                        ▼
        ┌─────────────────────────────────────────────────────┐
        │                    backend                           │
        │  Express · Socket.IO · Prisma                        │
        │  ┌─────────────┐  ┌────────────┐  ┌──────────────┐   │
        │  │ auth/JWT +  │  │  thread &  │  │  AI services │   │
        │  │ API keys    │  │  messages  │  │  (OpenAI)    │   │
        │  └─────────────┘  └────────────┘  └──────────────┘   │
        │  ┌─────────────────────────┐  ┌────────────────┐     │
        │  │ paint state scoring     │  │ vector store   │     │
        │  │ (context relevance)     │  │ sync           │     │
        │  └─────────────────────────┘  └────────────────┘     │
        └────────────────────────┬─────────────────────────────┘
                                 │
                          ┌──────▼──────┐
                          │ PostgreSQL  │
                          └─────────────┘
```

---

## License

[MIT](./LICENSE) © 2026 OperatorApp
