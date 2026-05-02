<div align="center">

```
 ██████╗ ██████╗ ██████╗ ███████╗██████╗ ██╗  ██╗
██╔════╝██╔═══██╗██╔══██╗██╔════╝██╔══██╗╚██╗██╔╝
██║     ██║   ██║██║  ██║█████╗  ██████╔╝ ╚███╔╝ 
██║     ██║   ██║██║  ██║██╔══╝  ██╔══██╗ ██╔██╗ 
╚██████╗╚██████╔╝██████╔╝███████╗██║  ██║██╔╝ ██╗
 ╚═════╝ ╚═════╝ ╚═════╝ ╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝
```

**AI-Augmented Competitive Programming · Zero Human Curation · Fully Autonomous Judge**

<br/>

[![Microservices](https://img.shields.io/badge/Microservices-6-0D1B2A?style=flat-square&logo=kubernetes&logoColor=00F5D4)](.)
[![Status](https://img.shields.io/badge/Status-85%25_Complete-0D1B2A?style=flat-square&logo=vercel&logoColor=00F5D4)](.)
[![LLM](https://img.shields.io/badge/LLM-Groq_LLaMA3-0D1B2A?style=flat-square&logo=meta&logoColor=00F5D4)](.)
[![Judge](https://img.shields.io/badge/Judge-Docker_Sandboxed-0D1B2A?style=flat-square&logo=docker&logoColor=00F5D4)](.)
[![Vector DB](https://img.shields.io/badge/Vector_DB-AstraDB-0D1B2A?style=flat-square&logoColor=00F5D4)](.)

<br/>

> *Most coding platforms rely on humans to write problems. coderX eliminates that bottleneck entirely —*  
> *an LLM generates problems on demand, a vector engine prevents duplicates, and an isolated Docker judge evaluates every submission in real time.*

</div>

---

## ◈ Microservice Repositories

> This repo is documentation-only. All services live in their own repositories.

| Service | Repository | Stack |
|:--------|:-----------|:------|
| 🤖 **AI Service** | [coderX_aiService](https://github.com/IamAbhinav01/coderX_aiService) | Python · FastAPI · Groq · LangChain · AstraDB |
| 📝 **Problem Service** | [coderX---Problem_Service](https://github.com/IamAbhinav01/coderX---Problem_Service) | Node.js · Express · MongoDB |
| 📤 **Submission Service** | [coderX---submissionService](https://github.com/IamAbhinav01/coderX---submissionService) | Node.js · Fastify · BullMQ · Redis |
| ⚡ **Evaluator Service** | [coderX--Evaluator-Service](https://github.com/IamAbhinav01/coderX--Evaluator-Service) | TypeScript · Docker · Dockerode |
| 🔌 **WebSocket Service** | [coderX_WebSocketService](https://github.com/IamAbhinav01/coderX_WebSocketService)| Node.js · Socket.io · Redis |
| 🎨 **Frontend** | [coderX_FrontEnd](https://github.com/IamAbhinav01/coderX_FrontEnd) | React 19 · TypeScript · Vite · Tailwind |

---

## ◈ System Architecture

<br/>

<!-- ═══════════════════════════════════════════════════════════ -->
<!--           ARCHITECTURE DIAGRAM — ADD YOUR OWN HERE         -->
<!-- ═══════════════════════════════════════════════════════════ -->

<br/>
<img width="6350" height="3021" alt="architecture" src="https://github.com/user-attachments/assets/a028d69c-70e9-49a4-a4f7-e4eeaedd5ac0" />

<br/>
<br/>

---

## ◈ How It Works

### `[1/3]` — LLM Problem Generation

The AI Service hits **Groq's API via LangChain** to produce fully-structured problems from a simple `{topic, difficulty}` prompt. Groq's LLaMA-3 inference runs at ~10× lower latency than OpenAI — critical when generation is a synchronous user action.

```python
chain = prompt_template | groq_llm | output_parser
problem = chain.invoke({ "topic": "Binary Trees", "difficulty": "Hard" })
# → { title, description, test_cases, editorial, time_complexity }
```

### `[2/3]` — Semantic Deduplication

Before any problem is stored, it is embedded via **Voyage AI** and compared against every existing problem in **AstraDB** (a persistent, serverless Cassandra-backed vector store). Problems exceeding a tuned similarity threshold are rejected — the deduplication index survives restarts and scales automatically, unlike FAISS.

```python
embedding = voyage_client.embed([problem.description])
similar   = astra_collection.find(sort={"$vector": embedding}, limit=5, projection={"$similarity": True})

if similar[0]["$similarity"] > DEDUP_THRESHOLD:
    raise DuplicateProblemError("Too similar to existing problem")
```

> The threshold was manually tuned by testing against known near-duplicate problems.

### `[3/3]` — Isolated Execution & Judging

Each submission is enqueued into **BullMQ** (Redis-backed) and picked up by a worker in the Evaluator Service. The worker spins up a **fresh Docker container per submission**, injects user code via Heredocs (preventing shell injection), enforces hard resource limits, and streams the verdict back through **Socket.io**.

```typescript
const result = await containerFactory.run({
  language:    'python',
  code:        submission.code,
  testCases:   problem.testCases,
  timeLimit:   2000,   // ms
  memoryLimit: 256,    // MB
});
// Handles: TLE · MLE · OOM kills · zombie process cleanup
```

---

## ◈ Current Status

```
████████████████████░░░░  85% Complete
```

| Area | Status | Notes |
|:-----|:------:|:------|
| Judge Engine (Docker sandbox) | ✅ Done | TLE, MLE, OOM, shell injection handled |
| AI Problem Generation Pipeline | ✅ Done | Groq + LangChain + structured output |
| Semantic Deduplication | ✅ Done | Voyage AI + AstraDB, threshold tuned |
| BullMQ Submission Queue | ✅ Done | Bull Board monitoring integrated |
| WebSocket Verdict Streaming | ✅ Done | Real-time testcase-level feedback |
| Multi-Language Support | ✅ Done | C++, Java, Python |
| Frontend UI + Monaco Editor | ✅ Done | Triple-editor with locked boilerplate |
| Glassmorphism Design System | ✅ Done | Tailwind-based |
| Generate Button → AI Service | 🔧 In Progress | Final integration wire-up |
| JWT Authentication | 🔧 Planned | Replacing demo IDs (Firebase / Clerk) |
| User Dashboard & Stats | 🔧 Planned | Heatmaps, solved count from DB |

---

## ◈ Technical Decisions

<details>
<summary><b>Why BullMQ over direct execution?</b></summary>

<br/>

Submission spikes would overwhelm the judge if handled synchronously. BullMQ decouples ingestion from execution — evaluator workers can be scaled horizontally, independently of the API layer.

</details>

<details>
<summary><b>Why AstraDB over FAISS?</b></summary>

<br/>

FAISS requires an in-memory index that must be rebuilt on every restart. AstraDB is persistent and serverless — the deduplication index is always live, and storage scales automatically without ops overhead.

</details>

<details>
<summary><b>Why Groq over OpenAI?</b></summary>

<br/>

Problem generation is a synchronous API call that the user waits for. Groq's LLaMA-3 inference latency is ~10× lower than GPT-4 equivalents, making the UX significantly snappier for this use case.

</details>

<details>
<summary><b>Why Heredocs for code injection?</b></summary>

<br/>

Interpolating user code directly into shell commands opens shell injection attacks. Heredocs write code to stdin as a raw stream — the container's shell never interprets the user's content as a command.

</details>

---

## ◈ Tech Stack

| Layer | Technology |
|:------|:-----------|
| **Frontend** | React 19 · TypeScript · Vite · Tailwind CSS · Monaco Editor |
| **Problem Service** | Node.js · Express · MongoDB |
| **Submission Service** | Node.js · Fastify · BullMQ · Redis |
| **AI Service** | Python · FastAPI · LangChain · Groq (LLaMA-3) |
| **Embeddings** | Voyage AI — 1024-dim vectors |
| **Vector Store** | AstraDB (Cassandra-backed, serverless) |
| **Judge** | TypeScript · Docker · Dockerode |
| **Real-time** | Socket.io · Redis pub/sub |
| **Monitoring** | Bull Board (BullMQ dashboard) |

---

## ◈ API Reference

### AI Service

```
POST /api/v1/problems/generate
     Body    → { "topic": string, "difficulty": "Easy" | "Medium" | "Hard" }
     Returns → { problem, embedding_stored, dedup_passed }

GET  /api/v1/problems/
     Returns → paginated list of generated problems

GET  /health
     Returns → service status
```

### Submission Flow

```
POST /api/submissions          → enqueued to BullMQ, returns submission ID
GET  /api/submissions/:id      → polling endpoint for verdict
WS   /ws/submissions/:id       → real-time testcase-level verdict stream
```

### Monitoring

```
GET  http://localhost:3002/admin/queues   → Bull Board queue dashboard
```

---

## ◈ Running Locally

### Prerequisites

```
Node.js 18+    Python 3.10+    Docker    Redis    MongoDB
Groq API key · AstraDB credentials · Voyage AI key
```

### Boot Order

```bash
# 1 — AI Service
cd coderX_aiService && pip install -r requirements.txt
uvicorn main:app --reload --port 8001

# 2 — Problem Service
cd coderX---Problem_Service && npm install && npm start          # :3001

# 3 — Submission Service
cd coderX---submissionService && npm install && npm start        # :3002

# 4 — Evaluator Service (queue consumer, no HTTP port)
cd coderX--Evaluator-Service && npm install && npm run dev

# 5 — Frontend
cd coderX_FrontEnd && npm install && npm run dev                 # :5173
```

---

## ◈ What I Built End-to-End

This is not a tutorial project. Every architectural decision was designed and implemented from scratch:

- Designed the microservices split — which service owns what, and why
- Chose AstraDB over FAISS after benchmarking persistence and restart behavior
- Tuned the deduplication threshold manually against real near-duplicate test cases
- Solved real Docker edge cases: container cleanup, OOM kills, zombie processes, Heredoc injection safety
- Integrated Bull Board for live queue health monitoring
- Built a triple-pane Monaco editor where boilerplate is read-only to prevent wrapper breakage

---

<div align="center">

**Abhinav Sunil** — CSE (AI/ML), Lovely Professional University · Grad 2027

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0D1B2A?style=flat-square&logo=linkedin&logoColor=00F5D4)](https://www.linkedin.com/in/abhinav-sunil-870184279/)
[![GitHub](https://img.shields.io/badge/GitHub-0D1B2A?style=flat-square&logo=github&logoColor=00F5D4)](https://github.com/IamAbhinav01)
[![Email](https://img.shields.io/badge/Email-0D1B2A?style=flat-square&logo=gmail&logoColor=00F5D4)](mailto:abhinavsunil@hotmail.com)

<br/>

*Code. Compete. Conquer.*

</div>
