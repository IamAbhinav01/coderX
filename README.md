<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Orbitron&weight=900&size=48&duration=2500&pause=800&color=6C63FF&center=true&vCenter=true&width=1000&height=80&lines=⚡+coderX;AI-Powered+Coding+Platform;Where+Intelligence+Meets+Code" alt="CoderX Typing SVG" />
</p>

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Inter&weight=500&size=18&duration=3000&pause=1000&color=A78BFA&center=true&vCenter=true&width=900&lines=Microservices+Architecture+%7C+LLM+Problem+Generation+%7C+Sandboxed+Code+Execution;Semantic+Deduplication+%7C+BullMQ+Job+Queues+%7C+Docker+Isolation;Built+by+engineers%2C+for+competitive+programmers" />
</p>

<br/>

<p align="center">
  <!-- Architecture -->
  <img src="https://img.shields.io/badge/Architecture-Microservices-6C63FF?style=for-the-badge&logo=serverless&logoColor=white" />
  <img src="https://img.shields.io/badge/AI-LangChain_%7C_Groq_LLM-A78BFA?style=for-the-badge&logo=openai&logoColor=white" />
  <img src="https://img.shields.io/badge/Execution-Docker_Sandbox-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/Queue-BullMQ_%7C_Redis-FF6B6B?style=for-the-badge&logo=redis&logoColor=white" />
  <br/>
  <!-- Tech stack -->
  <img src="https://img.shields.io/badge/Frontend-React_19_%7C_Vite_%7C_TypeScript-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/Backend-Node.js_%7C_Express_%7C_Fastify-339933?style=for-the-badge&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/AI_Service-FastAPI_%7C_Python-009688?style=for-the-badge&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/DB-MongoDB_%7C_AstraDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white" />
  <br/>
  <!-- Status -->
  <img src="https://img.shields.io/badge/Status-Active_Development-22C55E?style=for-the-badge&logo=git&logoColor=white" />
  <img src="https://img.shields.io/badge/License-MIT-F59E0B?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Author-Abhinav_Sunil-EC4899?style=for-the-badge&logo=github&logoColor=white" />
</p>

---

<br/>

## 🌌 What is coderX?

**coderX** is a production-grade, AI-augmented competitive programming platform built on a fully decoupled **microservices architecture**. It acts as an autonomous problem-setter and judge — generating unique coding challenges with Large Language Models, deduplicating them via vector similarity search, and evaluating code submissions by running them inside isolated Docker containers.

Think **LeetCode**, but with a backend that _writes its own problems_ and a judge that _spins up fresh containers_ for every submission.

<br/>

> **🧠 Intelligence Layer** → Groq LLM + LangChain generates problems from a topic + difficulty prompt
>
> **🔍 Semantic Guard** → AstraDB vector search prevents near-duplicate problems from ever being stored
>
> **⚡ Judge System** → BullMQ job queue feeds code payloads to language-specific Docker executors
>
> **🖥️ Developer UX** → React 19 + Vite + Tailwind frontend for fast, modern problem solving

<br/>

---

## 🏗️ System Architecture

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'primaryColor': '#6C63FF', 'edgeLabelBackground': '#1e1b4b', 'clusterBkg': '#1e293b', 'titleColor': '#A78BFA'}}}%%
graph TD
    U(("👤 User")):::user

    subgraph FE ["🌐 Frontend Layer — React 19 + Vite + Tailwind"]
        R["⚛️ React Dashboard<br/><small>coderX_FrontEnd</small>"]
    end

    subgraph GATE ["🔀 API Gateway Layer"]
        SS["📡 Submission Service<br/><small>Fastify · Port 3000</small>"]
        PS["📚 Problem Service<br/><small>Express · Port 3001</small>"]
    end

    subgraph AI ["🧠 AI Engine — FastAPI + Python"]
        AIS["🤖 AI Service<br/><small>FastAPI · Port 8000</small>"]
        GROQ["🔮 Groq LLM<br/><small>via LangChain</small>"]
        EMB["🧬 Embeddings<br/><small>sentence-transformers</small>"]
        ASTRA[("🗄️ AstraDB<br/><small>Vector Store</small>")]
    end

    subgraph EVAL ["⚙️ Evaluation Engine — TypeScript + Docker"]
        ES["🧪 Evaluator Service<br/><small>Express · BullMQ Worker</small>"]
        PY["🐍 Python Executor"]
        CPP["⚙️ C++ Executor"]
        JAVA["☕ Java Executor"]
    end

    subgraph STORE ["🗃️ Data Persistence"]
        REDIS[("🔴 Redis<br/><small>BullMQ Queue</small>")]
        MONGO[("🍃 MongoDB<br/><small>Problems + Submissions</small>")]
    end

    U -->|"Browse / Submit"| R
    R -->|"POST /api/submissions"| SS
    R -->|"GET /api/problems"| PS
    SS -->|"Persist + Enqueue"| REDIS
    SS -->|"Save Submission"| MONGO
    PS -->|"CRUD"| MONGO
    R -->|"POST /generate"| AIS
    AIS --> GROQ
    AIS --> EMB
    AIS <-->|"Vector Search + Upsert"| ASTRA
    REDIS -->|"Job Dequeue"| ES
    ES --> PY
    ES --> CPP
    ES --> JAVA
    PY & CPP & JAVA -->|"Ephemeral Docker Container"| ES

    classDef user fill:#6C63FF,stroke:#A78BFA,color:#fff,font-weight:bold
    classDef fe fill:#1e3a5f,stroke:#61DAFB,color:#61DAFB
    classDef gate fill:#1a2636,stroke:#A78BFA,color:#A78BFA
    classDef ai fill:#1e1b4b,stroke:#7C3AED,color:#C4B5FD
    classDef exec fill:#1a1a2e,stroke:#FF6B6B,color:#FCA5A5
    classDef store fill:#14231a,stroke:#22C55E,color:#86EFAC
    class R fe
    class SS,PS gate
    class AIS,GROQ,EMB,ASTRA ai
    class ES,PY,CPP,JAVA exec
    class REDIS,MONGO store
```

<br/>

---

## 📋 Microservices at a Glance

| # | Service | Role | Language | Port | Repo Link |
|---|---------|------|----------|------|-----------|
| 🧠 | **AI Service** | LLM problem generation + semantic dedup | Python / FastAPI | `8000` | [View Repo](#) |
| 📚 | **Problem Service** *(v1)* | Problem CRUD — original version | Node.js / Express | `3000` | [View Repo](#) |
| 📚 | **Problem Service** *(v2)* | Problem CRUD — refactored with Winston logging | Node.js / Express | `3000` | [View Repo](#) |
| 📡 | **Submission Service** | Accepts code submissions, enqueues to Redis | Node.js / Fastify | `3000` | [View Repo](#) |
| 🧪 | **Evaluator Service** | BullMQ worker — runs code in Docker | TypeScript / Express | `5000` | [View Repo](#) |
| 🌐 | **Frontend** | Developer-facing React SPA | React 19 + Vite | `5173` | [View Repo](#) |

<br/>

---

## 🔬 Deep Dive — Each Microservice

<br/>

---

### 🧠 AI Service — `coderX_aiService`

<p>
  <img src="https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/Python_3.10+-3776AB?style=flat-square&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/LangChain-1C3C3C?style=flat-square&logo=langchain&logoColor=white" />
  <img src="https://img.shields.io/badge/Groq-F55036?style=flat-square" />
  <img src="https://img.shields.io/badge/AstraDB-6C63FF?style=flat-square" />
  <img src="https://img.shields.io/badge/sentence--transformers-FF6B6B?style=flat-square" />
  <img src="https://img.shields.io/badge/Pydantic_v2-E92063?style=flat-square&logo=pydantic&logoColor=white" />
</p>

**Purpose:** This is the "autonomous problem-setter" of the platform. Given a `topic` and `difficulty`, it either retrieves a semantically similar problem from AstraDB (cache hit) or generates a brand new one via the Groq LLM and persists it with a vector embedding.

#### 📁 Directory Structure

```
coderX_aiService/
├── main.py                    ← FastAPI app entry point (ASGI)
├── pyproject.toml             ← uv-managed Python dependencies
├── app/
│   ├── config/
│   │   ├── langchainConfig.py ← Singleton ChatGroq client factory
│   │   ├── db.py              ← AstraDB connection setup
│   │   └── server.py          ← Environment variable loading
│   ├── routes/
│   │   └── problem_routes.py  ← POST /generate endpoint
│   ├── services/
│   │   └── question_generator.py ← Core generation pipeline
│   ├── prompts/
│   │   └── problemPrompt.py   ← LangChain PromptTemplate
│   ├── utils/
│   │   ├── embedder.py        ← Query & document embedding helpers
│   │   ├── response_parser.py ← LLM JSON response parser + validator
│   │   └── logger.py          ← Structured logging
│   ├── vector_store/
│   │   └── astra_store.py     ← ANN search + upsert into AstraDB
│   └── errors/
│       └── base_error.py      ← Custom HTTP error class
```

#### 🔄 Request Lifecycle (Problem Generation)

```mermaid
%%{init: {'theme': 'dark'}}%%
flowchart LR
    A([POST /generate\ntopic + difficulty]) --> B{Validate\nInputs}
    B -->|Invalid| ERR1([400 Bad Request])
    B -->|Valid| C[Build Query\nEmbedding\nsemantic vector]
    C --> D{ANN Search\nAstraDB}
    D -->|Cache HIT\nsimilarity > threshold| E([Return Cached Problem\nsource: 'cache'])
    D -->|Cache MISS| F[Format LangChain\nPromptTemplate]
    F --> G[Invoke Groq LLM\nvia ChatGroq]
    G -->|LLM Error| ERR2([502 AI Service Error])
    G --> H[Parse & Validate\nJSON Response]
    H --> I[Embed Document\nfor Storage]
    I --> J[Upsert to\nAstraDB]
    J -->|DB Error| ERR3([500 Server Error])
    J --> K([Return New Problem\nsource: 'generated'])
```

#### 🔑 Key Technical Decisions

| Decision | Rationale |
|----------|-----------|
| **Singleton `ChatGroq` client** | Avoids HTTP pool re-initialisation per request; keeps connections warm |
| **Query vs Document embeddings** | Separate embedding calls optimised for ANN search (query) vs storage (document) |
| **`parse_llm_response` validator** | Handles LLM JSON malformation (common with large editorials); strips markdown fences, retries parsing |
| **18 valid topics enforced** | Prevents prompt injection; constrains LLM scope to competitive programming domains |
| **AstraDB vector threshold** | Prevents near-duplicate problems from polluting the problem set |

#### ⚙️ Environment Variables

```bash
GROQ_API_KEY=gsk_...
GROQ_MODEL=llama3-70b-8192
GROQ_TEMPERATURE=0.7
GROQ_MAX_TOKENS=4096
ASTRA_DB_APPLICATION_TOKEN=AstraCS:...
ASTRA_DB_API_ENDPOINT=https://...
ASTRA_DB_NAMESPACE=coderx
ASTRA_COLLECTION_NAME=problems
```

#### 🚀 Run Locally

```bash
cd coderX_aiService
uv run uvicorn main:app --reload --port 8000
# Swagger UI → http://localhost:8000/docs
# ReDoc     → http://localhost:8000/redoc
```

#### 📡 API Endpoints

| Method | Path | Description | Response |
|--------|------|-------------|----------|
| `POST` | `/generate` | Generate or retrieve a problem | `{ source, problem }` |
| `GET` | `/health` | Liveness probe | `{ status: "ok" }` |
| `GET` | `/docs` | Swagger UI | — |
| `GET` | `/redoc` | ReDoc UI | — |

<br/>

---

### 📚 Problem Service v1 — `coderX---Problem_Service`

<p>
  <img src="https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/Express_v5-000000?style=flat-square&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/Mongoose-880000?style=flat-square" />
  <img src="https://img.shields.io/badge/marked-FF6B6B?style=flat-square" />
</p>

**Purpose:** The original RESTful CRUD backend for managing coding problems stored in MongoDB. Handles creation, retrieval, update, and deletion of `Problem` documents. Includes Markdown-to-HTML sanitization for problem descriptions via `marked` and `sanitize-html`.

#### 📁 Directory Structure

```
coderX---Problem_Service/
├── src/
│   ├── index.js              ← Express app bootstrap + DB connection
│   ├── config/
│   │   └── db.config.js      ← Mongoose connection factory
│   ├── models/
│   │   └── problem.model.js  ← Mongoose Problem schema
│   ├── routes/
│   │   ├── index.js          ← Router aggregator
│   │   └── v1/               ← Versioned API routes
│   ├── controllers/          ← Request/response handlers
│   ├── services/
│   │   └── problem.service.js ← Business logic layer
│   ├── repositories/         ← Data access layer (Mongoose ops)
│   ├── errors/               ← Custom error classes
│   └── utils/
│       └── ErrorHandler.js   ← Express error middleware
```

#### 🗃️ Problem Data Model

```javascript
Problem {
  title:       String  (required)
  description: String  (required, stored as sanitized HTML)
  difficulty:  String  enum['easy', 'medium', 'hard']
  testCases:   [{ input: String, output: String }]
  codeStubs:   [{
                  language: enum['python', 'java', 'cpp'],
                  startSnippet: String,
                  endSnippet:   String
               }]
  editorial:   String  (optional solution explanation)
  topic:       String  (e.g. "dynamic programming")
  createdAt:   Date    (auto-generated)
}
```

#### 🔄 Request Flow

```mermaid
%%{init: {'theme': 'dark'}}%%
sequenceDiagram
    participant C as Client
    participant R as Router (Express)
    participant Ctrl as Controller
    participant Svc as ProblemService
    participant Repo as ProblemRepository
    participant DB as MongoDB

    C->>R: POST /api/v1/problems
    R->>Ctrl: route handler
    Ctrl->>Svc: createProblem(data)
    Svc->>Svc: markdownToHtml(description)
    Svc->>Repo: createProblem(processedData)
    Repo->>DB: Problem.create(doc)
    DB-->>Repo: savedDoc
    Repo-->>Svc: savedDoc
    Svc-->>Ctrl: savedDoc
    Ctrl-->>C: 201 { problem }
```

#### 📡 API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/v1/problems` | Create a new problem |
| `GET` | `/api/v1/problems` | List all problems |
| `GET` | `/api/v1/problems/:id` | Get problem by ID |
| `PUT` | `/api/v1/problems/:id` | Update a problem |
| `DELETE` | `/api/v1/problems/:id` | Delete a problem |

#### 🚀 Run Locally

```bash
cd coderX---Problem_Service
npm install
npm run dev   # nodemon src/index.js on :3000
```

<br/>

---

### 📚 Problem Service v2 — `coderX_problem_service`

<p>
  <img src="https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/Express_v5-000000?style=flat-square&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/Winston-FF6B6B?style=flat-square" />
  <img src="https://img.shields.io/badge/winston--mongodb-47A248?style=flat-square" />
</p>

**Purpose:** An evolved, production-hardened version of the Problem Service. The architecture is identical to v1 but adds **structured Winston logging** with a MongoDB transport, meaning all log events are persisted to the database alongside the problem data — enabling audit trails and operational visibility.

#### 🔑 Differences from v1

| Feature | v1 | v2 |
|---------|----|----|
| Logging | `console.log` | Winston structured logger |
| Log transport | stdout only | stdout + MongoDB (winston-mongodb) |
| Log level control | None | `NODE_ENV`-aware |
| Dependency | — | `winston`, `winston-mongodb` |

#### 📁 Directory Structure

```
coderX_problem_service/
├── src/
│   ├── index.js              ← Express app bootstrap + DB connection
│   ├── config/               ← DB + server configs
│   ├── models/               ← Mongoose Problem schema (identical to v1)
│   ├── routes/               ← API route definitions
│   ├── controllers/          ← Request handlers
│   ├── services/
│   │   └── problem.service.js ← Business logic with Markdown processing
│   ├── repositories/         ← Data access layer
│   ├── errors/               ← Custom error types
│   └── utils/
│       ├── ErrorHandler.js   ← Global error middleware
│       └── markdownSanitiser.js ← marked + sanitize-html pipeline
```

#### 🚀 Run Locally

```bash
cd coderX_problem_service
npm install
MONGO_URI=mongodb://... npm run dev
```

<br/>

---

### 📡 Submission Service — `coderX_SubmissionService`

<p>
  <img src="https://img.shields.io/badge/Fastify_v5-000000?style=flat-square&logo=fastify&logoColor=white" />
  <img src="https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/BullMQ-FF6B6B?style=flat-square&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/Zod-3E67B1?style=flat-square" />
</p>

**Purpose:** The high-throughput ingestion gateway for code submissions. It uses **Fastify** (not Express) for its significantly higher request throughput, accepts a submission payload from the frontend, validates it with Zod, persists it to MongoDB with a `Pending` status, and dispatches a job to a **BullMQ queue** backed by Redis for the Evaluator to process.

#### 📁 Directory Structure

```
coderX_SubmissionService/
├── src/
│   ├── index.js              ← Fastify server bootstrap (port 3000)
│   ├── app.js                ← Plugin registration
│   ├── config/               ← Redis + DB config
│   ├── models/
│   │   └── submission.models.js ← Mongoose Submission schema
│   ├── routes/
│   │   └── api/              ← Versioned API routes
│   ├── controllers/          ← Fastify route handlers
│   ├── services/
│   │   ├── submission.service.js ← Core submission logic
│   │   └── servicePlugin.js  ← Fastify plugin wrapper
│   ├── producers/            ← BullMQ queue producers
│   ├── queue/                ← Queue initialization helpers
│   ├── repositories/         ← Mongoose data access layer
│   ├── errors/               ← HTTP error classes
│   └── validators/           ← Zod validation schemas
```

#### 🗃️ Submission Data Model

```javascript
Submission {
  userId:    String  (required)
  problemId: String  (required)
  code:      String  (required — the user's source code)
  language:  String  (required — 'python' | 'java' | 'cpp')
  status:    String  enum['Pending', 'Success', 'RE', 'TLE', 'MLE', 'WA']
                     default: 'Pending'
}
```

**Status Codes Explained:**

| Code | Meaning |
|------|---------|
| `Pending` | Enqueued, not yet evaluated |
| `Success` | All test cases passed |
| `RE` | Runtime Error |
| `TLE` | Time Limit Exceeded |
| `MLE` | Memory Limit Exceeded |
| `WA` | Wrong Answer |

#### 🔄 Submission Lifecycle

```mermaid
%%{init: {'theme': 'dark'}}%%
flowchart TD
    A([Client POST\n/api/submissions]) --> B{Zod\nValidation}
    B -->|Invalid Schema| ERR([400 Validation Error])
    B -->|Valid| C[Persist to MongoDB\nstatus: 'Pending']
    C --> D[Push Job to\nBullMQ Queue\n'paymentQueue']
    D --> E([202 Accepted\nsubmissionId])
    D -->|async| F[Evaluator Worker\ndequeues job]
    F --> G[Docker Code Execution]
    G --> H[Update status\nin MongoDB]
```

#### ⚙️ Environment Variables

```bash
REDIS_HOST=localhost
REDIS_PORT=6379
MONGO_URI=mongodb://...
PORT=3000
```

#### 🚀 Run Locally

```bash
cd coderX_SubmissionService
npm install
npm start   # nodemon src/index.js
```

<br/>

---

### 🧪 Evaluator Service — `coderX_EvaluatorService`

<p>
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Express_v5-000000?style=flat-square&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/BullMQ-FF6B6B?style=flat-square&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/Dockerode-2496ED?style=flat-square&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/Bull_Board-F59E0B?style=flat-square" />
  <img src="https://img.shields.io/badge/Zod-3E67B1?style=flat-square" />
</p>

**Purpose:** This is the **Judge** — the most infrastructure-intensive service in the platform. It runs as a BullMQ worker, consuming code submission jobs from Redis. For each job, it spins up a **fresh, isolated Docker container** for the target language (Python, C++, or Java), executes the code against the provided test case input, captures stdout/stderr, and returns the result. Containers are destroyed immediately after execution.

#### 📁 Directory Structure

```
coderX_EvaluatorService/
├── src/
│   ├── main.ts               ← Express server + Worker initialization
│   ├── config/
│   │   ├── server.config.ts  ← Server port + env config
│   │   ├── redis.config.ts   ← ioredis connection
│   │   └── BullBoard.config.ts ← Bull Board dashboard adapter
│   ├── containers/
│   │   ├── containerFactory.ts ← Generic Docker container spawner
│   │   ├── dockerHelper.ts   ← Docker stream decoder (stdout/stderr mux)
│   │   ├── pullImage.ts      ← Docker image puller
│   │   ├── pythonExecutor.ts ← Python-specific Docker execution
│   │   ├── cppExecutor.ts    ← C++-specific Docker execution
│   │   └── javaExecutor.ts   ← Java-specific Docker execution
│   ├── workers/
│   │   └── sampleWorker.ts   ← BullMQ Worker factory
│   ├── jobs/
│   │   └── sampleJob.ts      ← Job handler class
│   ├── queues/
│   │   └── sampleQueue.ts    ← Queue definition (paymentQueue)
│   ├── producers/
│   │   └── sampleQueueProducers.ts ← Job enqueuer
│   ├── routes/               ← HTTP API routes
│   ├── controllers/
│   │   ├── ping.controller.ts ← Health check endpoint
│   │   └── submission.controller.ts ← Submission status endpoint
│   ├── dtos/                 ← Data Transfer Objects
│   ├── types/
│   │   └── codeExecutor.ts   ← ExecutionResponse interface
│   ├── utils/
│   │   └── constants.ts      ← Docker image names (PYTHON_IMAGE, etc.)
│   └── validators/           ← Zod schemas
```

#### 🐳 Docker Execution Pipeline

```mermaid
%%{init: {'theme': 'dark'}}%%
flowchart TD
    JOB([BullMQ Job\n{ code, language, testCase }]) --> ROUTER{Language\nRouter}

    ROUTER -->|python| PY[PythonExecutor]
    ROUTER -->|cpp| CPP[CppExecutor]
    ROUTER -->|java| JAVA[JavaExecutor]

    PY --> PULL[pullImage\nif not cached]
    CPP --> PULL
    JAVA --> PULL

    PULL --> CREATE[containerFactory.ts\nspawn Docker container]
    CREATE --> RUN[container.start()]
    RUN --> STREAM[Capture Log Stream\nstdout + stderr]
    STREAM --> DECODE[dockerHelper.ts\nDecode Docker Multiplex Frame]
    DECODE -->|stderr present| ERR([RE — Runtime Error])
    DECODE -->|stdout only| SUCCESS([Success Response\noutput: string])
    SUCCESS & ERR --> CLEANUP[container.stop()\ncontainer.remove()]
```

#### 🔑 How Code Execution Works

Each executor follows this exact pattern:
```typescript
// Example: PythonExecutor (simplified)
1. Pull Docker image (python:3.11-alpine) if not present
2. Create container with command:
   sh -c "echo '<code>' > test.py && echo '<input>' | python3 test.py"
3. Start container & attach log stream (stdout + stderr)
4. Await stream 'end' event
5. Decode Docker multiplexed stream (8-byte header per frame)
6. If stderr → reject with runtime error
7. If stdout → resolve with output string
8. finally: container.stop() + container.remove()
```

#### 📊 Bull Board Dashboard

The service exposes an admin UI at `/admin/queues` (via `@bull-board/express`) for real-time queue monitoring — inspect pending, active, completed, and failed jobs.

#### 🚀 Run Locally

```bash
cd coderX_EvaluatorService
npm install
npm run dev   # ts-node src/main.ts via nodemon
# Bull Board → http://localhost:5000/admin/queues
```

#### ⚙️ Environment Variables

```bash
PORT=5000
REDIS_HOST=localhost
REDIS_PORT=6379
```

<br/>

---

### 🌐 Frontend — `coderX_FrontEnd`

<p>
  <img src="https://img.shields.io/badge/React_19-61DAFB?style=flat-square&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/Vite_8-646CFF?style=flat-square&logo=vite&logoColor=white" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Tailwind_CSS_v3-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white" />
</p>

**Purpose:** The developer-facing Single Page Application. Built with React 19, Vite 8, TypeScript, and Tailwind CSS. Currently implements a hero landing page showcasing the coderX brand and CTA buttons for problem generation and discovery.

#### 📁 Directory Structure

```
coderX_FrontEnd/
├── index.html                ← App shell
├── vite.config.ts            ← Vite config (@vitejs/plugin-react)
├── tailwind.config.js        ← Tailwind theme config
├── tsconfig.json             ← TypeScript base config
├── src/
│   ├── main.tsx              ← React DOM root mount
│   ├── App.tsx               ← Application root component
│   ├── App.css               ← Global base styles
│   ├── index.css             ← Tailwind directives
│   ├── assets/
│   │   └── hero.png          ← Hero section image
│   └── components/
│       ├── Hero.tsx          ← Landing hero section
│       └── Hero.module.css   ← CSS Modules for hero
```

#### 🖥️ UI Components

| Component | Description |
|-----------|-------------|
| `Hero` | Landing page hero with animated coderX branding, tagline, and CTAs |

#### 🌟 Hero Component Features

- Animated **coderX** brand with kinetic `X` effect (CSS animation)
- Tagline: *"Elevate your technical skills with AI-engineered coding interview questions tailored exactly to your seniority and target role."*
- CTA buttons: **Generate Problem** and **Learn More**
- Responsive layout with hero image panel

#### 🚀 Run Locally

```bash
cd coderX_FrontEnd
npm install
npm run dev   # Vite dev server on http://localhost:5173
```

<br/>

---

## ⚡ End-to-End Submission Workflow

```mermaid
%%{init: {'theme': 'dark', 'sequence': {'actorMargin': 50}}}%%
sequenceDiagram
    actor User
    participant FE as React Frontend
    participant SS as Submission Service<br/>(Fastify)
    participant MDB as MongoDB
    participant RQ as Redis / BullMQ
    participant ES as Evaluator Service<br/>(BullMQ Worker)
    participant DK as Docker Container

    User->>FE: Writes code + clicks Submit
    FE->>SS: POST /api/submissions<br/>{ userId, problemId, code, language }

    SS->>SS: Zod validation
    SS->>MDB: Insert Submission { status: 'Pending' }
    MDB-->>SS: submissionId

    SS->>RQ: Enqueue job to paymentQueue<br/>{ submissionId, code, language, testCases }
    SS-->>FE: 202 Accepted { submissionId }

    Note over RQ,ES: Async processing begins

    RQ->>ES: Dequeue job (BullMQ worker)
    ES->>ES: Route to language executor
    ES->>DK: Pull image if needed
    ES->>DK: Create + Start container<br/>echo code > file && run with input
    DK-->>ES: Log stream (stdout/stderr)
    ES->>ES: Decode Docker multiplex stream

    alt All test cases pass
        ES->>MDB: Update status → 'Success'
    else Runtime Error
        ES->>MDB: Update status → 'RE'
    else Wrong Answer
        ES->>MDB: Update status → 'WA'
    end

    ES->>DK: container.stop() + remove()
    FE->>SS: Poll for result / WebSocket update
    SS->>MDB: Fetch submission status
    SS-->>FE: { status: 'Success' | 'WA' | 'RE' ... }
    FE-->>User: Display verdict
```

<br/>

---

## 🧠 AI Problem Generation Workflow

```mermaid
%%{init: {'theme': 'dark'}}%%
flowchart LR
    A([User Request\ntopic='arrays'\ndifficulty='hard']) --> B

    subgraph AIS ["🤖 AI Service — FastAPI"]
        B[Validate Inputs\nZod-like guards] --> C
        C[Build semantic\nquery string] --> D
        D[sentence-transformers\nEmbed query → 768-dim vector] --> E
        E{ANN Search\nAstraDB\ncosine similarity}
    end

    E -->|"similarity > 0.92\nCACHE HIT"| DONE1([Return existing problem\nsource: 'cache'])
    E -->|"No match\nCACHE MISS"| F

    subgraph LLM ["🔮 LLM Pipeline"]
        F[Format LangChain\nPromptTemplate\ntopic + difficulty] --> G
        G[Invoke ChatGroq\nllama3-70b-8192] --> H
        H[response_parser.py\nStrip fences + parse JSON\nValidate schema] --> I
    end

    subgraph STORE ["🗄️ Persistence"]
        I[Embed problem text\ndocument vector] --> J
        J[AstraDB.insertOne\nproblem + $vector]
    end

    J --> DONE2([Return problem\nsource: 'generated'])
```

<br/>

---

## 🛠️ Tech Stack Summary

### Languages & Runtimes

| Technology | Used In | Purpose |
|------------|---------|---------|
| **Python 3.10+** | AI Service | FastAPI app, LLM integration, embeddings |
| **TypeScript** | Evaluator Service | Type-safe BullMQ workers, Docker executors |
| **JavaScript (ESM/CJS)** | Problem Service, Submission Service | Node.js backend services |
| **TSX / React** | Frontend | Component-based UI |

### Infrastructure & Databases

| Technology | Role |
|------------|------|
| **Docker** | Sandboxed code execution (Python, C++, Java containers) |
| **Redis** | BullMQ message broker for submission queues |
| **MongoDB + Mongoose** | Problem and submission persistence |
| **AstraDB (Cassandra)** | Vector database for semantic problem search |

### AI / ML Stack

| Technology | Role |
|------------|------|
| **Groq** | Ultra-fast LLM inference (llama3-70b) |
| **LangChain** | LLM orchestration, PromptTemplate management |
| **sentence-transformers** | Local embedding model (BAAI/bge-large-en-v1.5, 1024-dim) |

### Key Libraries

| Library | Service | Purpose |
|---------|---------|---------|
| `bullmq` | Submission + Evaluator | Job queue management |
| `dockerode` | Evaluator | Docker container API client |
| `@bull-board` | Evaluator | Queue monitoring dashboard |
| `fastify` | Submission | High-throughput HTTP server |
| `zod` | Submission + Evaluator | Runtime schema validation |
| `pydantic v2` | AI Service | Data validation + serialization |
| `marked` + `sanitize-html` | Problem Services | Markdown-to-HTML rendering |
| `winston` + `winston-mongodb` | Problem Service v2 | Structured log persistence |
| `astrapy` | AI Service | AstraDB Python SDK |

<br/>

---

## 🚀 Getting Started

### Prerequisites

```bash
# Required services
docker --version    # >= 20.x
redis-server        # >= 7.x
mongod              # >= 6.x

# Runtimes
node --version      # >= 20.x LTS
python --version    # >= 3.10
uv --version        # Python package manager (recommended)
```

### 1. Clone the Monorepo

```bash
git clone https://github.com/IamAbhinav01/coderX.git
cd coderX
```

### 2. Launch Infrastructure

```bash
# Start Redis
docker run -d -p 6379:6379 redis:7-alpine

# Start MongoDB
docker run -d -p 27017:27017 mongo:6
```

### 3. Start Each Service

```bash
# Terminal 1 — AI Service
cd coderX_aiService && cp .env.example .env   # fill in API keys
uv run uvicorn main:app --reload --port 8000

# Terminal 2 — Problem Service (v2)
cd coderX_problem_service && npm install
MONGO_URI=mongodb://localhost:27017/coderx npm run dev

# Terminal 3 — Submission Service
cd coderX_SubmissionService && npm install
npm start

# Terminal 4 — Evaluator Service
cd coderX_EvaluatorService && npm install
npm run dev

# Terminal 5 — Frontend
cd coderX_FrontEnd && npm install
npm run dev   # → http://localhost:5173
```

### Default Port Map

| Service | Port |
|---------|------|
| Frontend | `5173` |
| AI Service | `8000` |
| Problem Service | `3000` |
| Submission Service | `3000` |
| Evaluator Service | `5000` |
| Bull Board UI | `5000/admin/queues` |
| Redis | `6379` |
| MongoDB | `27017` |

<br/>

---

## 🔮 Roadmap

```mermaid
%%{init: {'theme': 'dark'}}%%
timeline
    title coderX Development Roadmap
    section Current
        Core Microservices  : AI Problem Generation
                            : Docker Code Execution
                            : BullMQ Job Queues
                            : React Frontend Shell
    section Short-term
        Auth System         : JWT Authentication
                            : User profiles + history
        Live Execution      : WebSocket real-time results
                            : Progress indicator per test case
    section Mid-term
        AI Hints            : Context-aware dynamic hints
                            : Based on submitted code analysis
        Leaderboards        : Elo-based global ranking
                            : Per-topic standings
    section Long-term
        ML Difficulty       : Auto-rank problems by pass rate
                            : Time/memory analytics
        Collaborative Mode  : Pair programming sessions
                            : Interview simulation rooms
```

<br/>

---

## 📊 Project Stats

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=IamAbhinav01&show_icons=true&theme=tokyonight&border_radius=12&hide_border=true&bg_color=0d1117&title_color=6C63FF&icon_color=A78BFA&text_color=c9d1d9" width="47%" alt="GitHub Stats"/>
  <img src="https://github-readme-streak-stats.herokuapp.com/?user=IamAbhinav01&theme=tokyonight&hide_border=true&background=0d1117&ring=6C63FF&fire=A78BFA&currStreakLabel=6C63FF" width="47%" alt="GitHub Streak"/>
</p>

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=IamAbhinav01&layout=compact&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=6C63FF&text_color=c9d1d9&langs_count=8" width="47%" alt="Top Languages"/>
</p>

<br/>

---

## 👨‍💻 Author

<p align="center">
  <b>Abhinav Sunil</b><br/>
  <i>AI Engineer · Backend Architect · Systems Builder</i><br/><br/>
  <a href="#">
    <img src="https://img.shields.io/badge/GitHub-IamAbhinav01-6C63FF?style=for-the-badge&logo=github" />
  </a>
  <a href="#">
    <img src="https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin" />
  </a>
  <a href="#">
    <img src="https://img.shields.io/badge/Portfolio-Visit-A78BFA?style=for-the-badge&logo=safari&logoColor=white" />
  </a>
</p>

<br/>

---

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Orbitron&weight=700&size=14&duration=4000&pause=1500&color=6C63FF&center=true&vCenter=true&width=600&lines=Built+with+%E2%9D%A4%EF%B8%8F+by+Abhinav+Sunil;If+coderX+helps+you%2C+drop+a+%E2%AD%90+on+GitHub!" />
</p>

<p align="center">
  <sub>© 2026 coderX — MIT License</sub>
</p>
