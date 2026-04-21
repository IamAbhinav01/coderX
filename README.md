# coderX Workspace

## Overview

This workspace contains multiple services that together form the `coderX` platform for coding problem generation, problem management, submission handling, evaluation, and user interface.

The repository is organized into the following main folders:

- `coderX_aiService/` — Python FastAPI microservice for automated coding problem generation using LangChain, Groq, embeddings, and AstraDB.
- `coderX_backend_service/` — Express.js backend for problem CRUD, MongoDB persistence, and API routing.
- `coderX_EvaluatorService/` — TypeScript service for code submission evaluation, BullMQ queue processing, and Docker-backed language runners.
- `coderX_FrontEnd/` — React + TypeScript + Vite frontend application.
- `coderX_SubmissionService/` — Fastify microservice with a simple ping API and fastify/CORS setup.

---

## Project Summaries

### 1. `coderX_aiService`

**What it does:**

- Generates new competitive programming problems using an LLM.
- Uses semantic embeddings to detect similar existing problems and avoid duplicate generation.
- Stores generated problems and vector embeddings in AstraDB.
- Provides a REST endpoint for problem generation.

**Key technologies:**

- Python 3.10+
- FastAPI
- LangChain
- Groq LLM
- Voyage AI embeddings
- AstraDB / DataStax
- Pydantic
- Uvicorn

**Core files:**

- `main.py` — FastAPI application entrypoint.
- `app/routes/problem_routes.py` — exposes `POST /api/v1/generate/problem`.
- `app/services/question_generator.py` — generation pipeline with similarity search.
- `app/prompts/problemPrompt.py` — problem prompt template.
- `app/vector_store/astra_store.py` — vector search and insert operations.
- `app/config/db.py` — AstraDB DataAPI client.
- `app/config/langchainConfig.py` — Groq LLM client configuration.

**Key endpoint:**

- `POST /api/v1/generate/problem`
  - Body includes `topic` and `difficulty`.
  - Returns either a cached similar problem or a newly generated problem.

**Run locally:**

```bash
cd coderX_aiService
uv run uvicorn main:app --reload --port 8000
```

---

### 2. `coderX_backend_service`

**What it does:**

- Serves as an Express.js backend for managing coding problems.
- Provides CRUD operations for problem records.
- Connects to MongoDB Atlas.
- Includes an intended AI route integration point for AI-generated problems.

**Key technologies:**

- Node.js / Express
- MongoDB Atlas via Mongoose
- body-parser
- Winston logging
- HTTP status codes utilities
- `marked` / `sanitize-html` for HTML processing

**Core files:**

- `src/index.js` — Express app startup and DB connection.
- `src/routes/index.js` — base router mounting `/api`.
- `src/routes/v1/index.js` — mounts `/api/v1/problems` and `/api/v1/ai`.
- `src/routes/v1/problems.routes.js` — problem CRUD endpoints.
- `src/controllers/problem.controller.js` — problem request handling.
- `src/config/db.config.js` — MongoDB Atlas connection.
- `src/utils/ErrorHandler.js` — global error handling middleware.

**Key endpoints:**

- `GET /api/v1/problems`
- `GET /api/v1/problems/:id`
- `POST /api/v1/problems`
- `PUT /api/v1/problems/:id`
- `DELETE /api/v1/problems/:id`

> Note: The backend imports `src/ai-question-generator/routes/aiProblemRoutes` for `/api/v1/ai`, but that module is not present in the current workspace structure.

**Run locally:**

```bash
cd coderX_backend_service
npm install
npm run dev
```

---

### 3. `coderX_EvaluatorService`

**What it does:**

- Hosts evaluation logic for code submissions.
- Uses BullMQ and Redis for queue-based job processing.
- Exposes a queue admin interface at `/admin/queues`.
- Starts a sample worker and invokes language-specific runner containers for Python, C++, and Java.

**Key technologies:**

- TypeScript
- Express
- BullMQ
- Bull Board
- Dockerode
- Redis / ioredis
- Zod for validation
- dotenv

**Core files:**

- `src/main.ts` — Express app entrypoint and queue admin router.
- `src/routes/index.ts` — main API router.
- `src/routes/v1/index.ts` — versioned API router with ping and submissions routes.
- `src/routes/v1/submission.Route.ts` — submission creation endpoint.
- `src/controllers/submission.controller.ts` — submission handling logic.
- `src/containers/` — container runner helpers for Python, C++, Java.
- `src/config/redis.config.ts` — Redis settings.
- `src/config/BullBoard.config.ts` — Bull Queue admin setup.

**Key endpoints:**

- `GET /api/v1/ping-check`
- `POST /api/v1/submissions`
- Queue UI: `/admin/queues`

**Run locally:**

```bash
cd coderX_EvaluatorService
npm install
npm run dev
```

---

### 4. `coderX_FrontEnd`

**What it does:**

- Provides a frontend interface built with React and Vite.
- Likely intended to consume backend APIs for problem display, generation, and submission workflows.

**Key technologies:**

- React
- TypeScript
- Vite
- Tailwind CSS
- ESLint

**Core files:**

- `src/main.tsx` — frontend application bootstrap.
- `src/App.tsx` — main application component.
- `src/index.css` / `src/App.css` — styling.

**Run locally:**

```bash
cd coderX_FrontEnd
npm install
npm run dev
```

---

### 5. `coderX_SubmissionService`

**What it does:**

- Hosts a lightweight Fastify microservice.
- Registers CORS and exposes versioned API routes.
- Currently provides a ping health endpoint.

**Key technologies:**

- Node.js / Fastify
- Fastify plugin system
- @fastify/cors
- BullMQ
- Redis
- Mongoose

**Core files:**

- `src/index.js` — Fastify startup and app registration.
- `src/app.js` — registers CORS and API routes.
- `src/routes/api/api.routes.js` — mounts `/api/v1`.
- `src/routes/api/v1/v1.routes.js` — mounts `/ping`.
- `src/routes/api/v1/ping.routes.js` — returns `pong`.

**Key endpoint:**

- `GET /api/v1/ping/`

**Run locally:**

```bash
cd coderX_SubmissionService
npm install
npm start
```

---

## How these services fit together

- `coderX_aiService` is responsible for generating and caching coding problems using AI and vector search.
- `coderX_backend_service` is the main management API for problems and stores problem data in MongoDB.
- `coderX_EvaluatorService` appears to handle submission evaluation and containerized execution.
- `coderX_FrontEnd` is the user-facing React application.
- `coderX_SubmissionService` contains a lightweight Fastify service with a ping route and potential submission/queue capabilities.

## Notes

- There is no single monorepo orchestration file, so each folder is a separately runnable service.
- `coderX_backend_service` references an AI route module that is not present in the current workspace.
- The generated README here is based on the current workspace structure and source files.

---

## Recommended next step

If you want, I can also create a single `docker-compose.yml` or a workspace `CONTRIBUTING.md` to coordinate these services together.
