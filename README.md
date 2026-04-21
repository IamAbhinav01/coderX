🚀 coderX Workspace<p align="center"><img src="https://readme-typing-svg.herokuapp.com?font=Orbitron&size=32&duration=3000&color=00F7FF&center=true&vCenter=true&width=900&lines=AI-POWERED+CODING+PLATFORM;MICROSERVICES+ARCHITECTURE;SEMANTIC+DEDUPLICATION+VIA+FAISS;BUILT+FOR+COMPETITIVE+PROGRAMMERS" /></p><p align="center"><img src="https://img.shields.io/badge/Architecture-Microservices-00F7FF?style=for-the-badge&logo=architecture" /><img src="https://img.shields.io/badge/Stack-MERN%20+%20FastAPI-61DAFB?style=for-the-badge&logo=react" /><img src="https://img.shields.io/badge/AI-LangChain%20%7C%20LLMs-white?style=for-the-badge&logo=chainlink" /><img src="https://img.shields.io/badge/Infrastructure-Docker-2496ED?style=for-the-badge&logo=docker" /></p>🌌 About coderXcoderX is an advanced, distributed ecosystem designed to revolutionize competitive programming. By leveraging Large Language Models (LLMs) and Vector Databases, it automates the creation of high-quality coding challenges while ensuring a secure, scalable execution environment.💎 Core Value Propositions🧠 Intelligent Generation: Automated problem creation with LLMs.📚 Semantic Integrity: Prevents duplicate problems using vector similarity search.⚡ Isolated Execution: Secure code evaluation using ephemeral Docker containers.🖥️ Production-Ready UI: Responsive React dashboard for real-time problem-solving.🏗️ System ArchitectureThe platform is built on a decoupled microservices architecture to ensure horizontal scalability and fault tolerance.Code snippetgraph TD
subgraph Client_Layer [Frontend Layer]
U[User Browser] --> React[React + Vite Dashboard]
end

    subgraph API_Gateway [Traffic Control]
        React --> SS[Submission Service - Fastify]
        React --> BS[Backend Service - Express]
    end

    subgraph AI_Engine [AI & Vector Search]
        BS --> AIS[AI Service - FastAPI]
        AIS --> Astra[AstraDB / FAISS]
        AIS --> LLM[Groq / LangChain]
    end

    subgraph Execution_Engine [Judge System]
        SS --> Redis[(Redis Queue)]
        Redis --> Eval[Evaluator Service - TS]
        Eval --> Docker[[Docker Runners]]
    end

    subgraph Data_Persistence [Storage]
        BS --> Mongo[(MongoDB)]
    end

    %% Styling
    style Client_Layer fill:#1a1a1a,stroke:#00F7FF,stroke-width:2px
    style AI_Engine fill:#1a1a1a,stroke:#7000FF,stroke-width:2px
    style Execution_Engine fill:#1a1a1a,stroke:#FF007A,stroke-width:2px

⚡ Microservices OverviewServiceResponsibilityTech Stack🧠 AI ServiceGenerates problems & semantic searchFastAPI, LangChain, Groq⚙️ BackendCore CRUD & User managementNode.js, Express, MongoDB🧪 EvaluatorSecure sandbox executionTypeScript, BullMQ, Docker📡 SubmissionHigh-concurrency routingFastify, Redis🌐 FrontendDeveloper ExperienceReact, TypeScript, Tailwind🛠️ Deep Dive: Services🧠 coderX AI ServicePurpose: Automates the "Problem Setter" role. It creates unique problems and checks if they are "semantically similar" to existing ones to maintain platform quality.Features: Vector Embeddings, AstraDB integration, LLM Prompt Engineering.Bashcd coderX_aiService && uvicorn main:app --reload --port 8000
🧪 coderX Evaluator ServicePurpose: The "Judge." It pulls submissions from a Redis queue and runs them against test cases in an isolated environment.Features: Multi-language support (Python, C++, Java), Containerized isolation, Bull Board monitoring.Bashcd coderX_EvaluatorService && npm run dev
📊 Repository Stats & Activity<p align="center"><img src="https://github-readme-stats.vercel.app/api?username=IamAbhinav01&show_icons=true&theme=tokyonight&border_radius=10&hide_border=true" width="48%"/><img src="https://github-readme-streak-stats.herokuapp.com/?user=IamAbhinav01&theme=tokyonight&hide_border=true" width="48%"/></p>🔮 Roadmap & Future Improvements[ ] 🤖 AI Hint System: Dynamic hints based on user code logic.[ ] 🧠 Difficulty ML: Automatically rank problems based on pass/fail rates.[ ] 🏆 Global Leaderboards: Real-time Elo-based ranking system.[ ] 📊 Analytics: Granular insights into memory usage and execution time.[ ] ⚡ Live Execution: WebSocket-based "Run" results for instant feedback.👨‍💻 AuthorAbhinav Sunil AI Engineer | Backend Developer | Systems Builder<p align="center"><b>If you find coderX useful, give it a ⭐ to support the development!</b></p>
