# ⚡ coderX — AI-Augmented Competitive Programming Platform

> **A production-grade, microservices-based coding platform where an LLM writes the problems, a vector engine deduplicates them, and Docker containers judge every submission — end to end, fully automated.**

---

## 🧠 Why This Exists

Most coding platforms like LeetCode rely on human-curated problem sets. coderX flips that — an **AI service generates problems on demand** from a topic + difficulty prompt, a **vector similarity engine** ensures no near-duplicate ever gets stored, and an **isolated Docker judge** evaluates submissions in real-time.

The goal: a fully autonomous, scalable problem-setting and judging pipeline — with zero human bottlenecks.

---

## 🏗️ System Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                        coderX Platform                               │
│                                                                      │
│   React 19 Frontend (TypeScript + Vite)                              │
│        │                                                             │
│        ▼                                                             │
│   ┌─────────────┐    ┌──────────────────┐    ┌──────────────────┐   │
│   │  Problem     │    │  Submission       │    │  AI Service      │   │
│   │  Service     │    │  Service          │    │  (FastAPI/Python) │   │
│   │  (Node.js)   │    │  (Node.js)        │    │                  │   │
│   └──────┬──────┘    └────────┬─────────┘    └────────┬─────────┘   │
│          │                    │                        │              │
│          ▼                    ▼                        ▼              │
│       MongoDB           BullMQ Queue            Groq LLM             │
│                         (Redis-backed)          + LangChain          │
│                                │                        │            │
│                                ▼                        ▼            │
│                         Evaluator Service         AstraDB            │
│                         (TypeScript)              (Vector Store)     │
│                         Docker Sandbox            Voyage AI          │
│                         (per submission)          (Embeddings)       │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 🤖 AI Service — The Intelligence Layer

The AI Service (`coderX_aiService`) is the most technically complex component. It's a standalone Python microservice that does three things:

### 1. LLM-Powered Problem Generation

Uses **Groq API via LangChain** to generate structured coding problems from a topic + difficulty prompt:

```python
# LangChain chain: prompt → Groq LLM → structured output
chain = prompt_template | groq_llm | output_parser
problem = chain.invoke({"topic": "Binary Trees", "difficulty": "Hard"})
# Returns: title, description, test_cases, editorial, complexity
```

### 2. Semantic Deduplication via Vector Search

Before storing any problem, the service checks for near-duplicates using **Voyage AI embeddings + AstraDB vector search**:

```python
# Embed the new problem
embedding = voyage_client.embed([problem.description])

# Search AstraDB for similar problems (cosine similarity)
similar = astra_collection.find(
    sort={"$vector": embedding},
    limit=5,
    projection={"$similarity": True}
)

# Reject if similarity > threshold
if similar[0]["$similarity"] > DEDUP_THRESHOLD:
    raise DuplicateProblemError("Too similar to existing problem")
```

### 3. Vector Storage with 1024-dim Embeddings

Problems that pass deduplication are stored in **AstraDB** with their embeddings for future similarity checks — enabling the system to stay non-redundant as the problem bank grows.

---

## ⚡ Evaluator Service — The Judge

The Evaluator Service (`coderX--Evaluator-Service`) runs submitted code inside **isolated Docker containers**:

- **BullMQ job queue** (Redis-backed) receives submission payloads
- A fresh Docker container is spun up per submission
- Code executes in sandboxed environments: Python, Java, C++
- Resource limits enforced (CPU, memory, time limits)
- `stdout`/`stderr` streamed back in real time

```typescript
// TypeScript: spin up container, run code, return verdict
const result = await containerFactory.run({
  language: 'python',
  code: submission.code,
  testCases: problem.testCases,
  timeLimit: 2000, // ms
  memoryLimit: 256, // MB
});
```

**This is the observability layer** — every execution is logged, timed, and tracked. Failed runs, TLE, MLE, and wrong answers are all captured with structured metadata.

---

## 🔍 Key Technical Decisions

### Why BullMQ over direct execution?

Submission spikes would overwhelm the judge. BullMQ decouples ingestion from execution, enabling horizontal scaling of the evaluator workers independently.

### Why AstraDB over FAISS?

FAISS requires in-memory index rebuilds on restart. AstraDB is a persistent, serverless vector store — the deduplication index survives service restarts and scales automatically.

### Why Groq over OpenAI?

Groq's inference latency is ~10x lower for LLaMA3 models. Problem generation is a synchronous API call — low latency matters for UX.

---

## 🛠️ Full Tech Stack

| Layer                   | Technology                                          |
| ----------------------- | --------------------------------------------------- |
| **Frontend**            | React 19, TypeScript, Vite, Tailwind CSS            |
| **Problem Service**     | Node.js, Express.js, MongoDB, REST API              |
| **Submission Service**  | Node.js, BullMQ, Redis, MongoDB                     |
| **AI Service**          | Python, FastAPI, LangChain, Groq LLM                |
| **Vector Store**        | AstraDB (DataStax), Voyage AI embeddings (1024-dim) |
| **Judge / Evaluator**   | TypeScript, Docker, dockerode, BullMQ workers       |
| **Languages Supported** | Python, Java, C++                                   |
| **Monitoring**          | Bull Board (queue dashboard), structured logging    |

---

## 🚀 Running Locally

### Prerequisites

- Node.js 18+, Python 3.10+, Docker
- Redis (for BullMQ)
- Groq API key, AstraDB credentials, Voyage AI key

### 1. AI Service

```bash
cd coderX_aiService
pip install -r requirements.txt
# Set env vars: GROQ_API_KEY, ASTRA_DB_TOKEN, VOYAGE_API_KEY
uvicorn main:app --reload --port 8001
```

### 2. Problem Service

```bash
cd coderX---Problem_Service
npm install
npm start  # port 3001
```

### 3. Submission Service

```bash
cd coderX---submissionService
npm install
npm start  # port 3002
```

### 4. Evaluator Service

```bash
cd coderX--Evaluator-Service
npm install
npm run dev  # consumes from BullMQ
```

### 5. Frontend

```bash
cd coderX_FrontEnd
npm install
npm run dev  # port 5173
```

---

## 📡 API Reference

### AI Service Endpoints

```
POST /api/v1/problems/generate
  Body: { "topic": "string", "difficulty": "Easy|Medium|Hard" }
  Returns: { problem, embedding_stored, dedup_passed }

GET  /api/v1/problems/
  Returns: paginated list of generated problems

GET  /health
  Returns: service status
```

### Submission Flow

```
POST /api/submissions        → enqueues to BullMQ
GET  /api/submissions/:id    → polls verdict
WS   /ws/submissions/:id     → real-time verdict stream (planned)
```

---

## 💡 What I Built End-to-End

This isn't a tutorial project. Every service — AI generation, semantic dedup, job queueing, Docker sandboxing, and the React frontend — was designed and implemented from scratch:

- Designed the **microservices split** (which service owns what)
- Chose **AstraDB over FAISS** after benchmarking persistence requirements
- Tuned the **deduplication threshold** by testing against similar problems
- Dealt with **real Docker edge cases**: container cleanup on TLE, OOM kills, zombie processes
- Built **Bull Board integration** to monitor queue health in dev

---

## 🔗 Related Repos

This is a multi-repo project. Each service is in its own repository:

| Service               | Repo                                                                                     |
| --------------------- | ---------------------------------------------------------------------------------------- |
| AI Problem Generation | [coderX_aiService](https://github.com/IamAbhinav01/coderX_aiService)                     |
| Problem CRUD API      | [coderX---Problem_Service](https://github.com/IamAbhinav01/coderX---Problem_Service)     |
| Submission Queue      | [coderX---submissionService](https://github.com/IamAbhinav01/coderX---submissionService) |
| Docker Judge          | [coderX--Evaluator-Service](https://github.com/IamAbhinav01/coderX--Evaluator-Service)   |
| Frontend              | [coderX_FrontEnd](https://github.com/IamAbhinav01/coderX_FrontEnd)                       |

---

## 👨‍💻 Author

**Abhinav Sunil** — CSE (AI/ML), Lovely Professional University · Grad 2027  
[LinkedIn](https://www.linkedin.com/in/abhinav-sunil-870184279/) · [GitHub](https://github.com/IamAbhinav01) · abhinavsunil@hotmail.com
