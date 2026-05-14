# 🤖 AI / ML Architecture Examples

Four production-style AI/ML system diagrams with complete Mermaid code.

---

## 1. RAG Pipeline (Retrieval-Augmented Generation)

The backbone of most production AI chatbots and knowledge-base assistants.

```mermaid
flowchart TD
    subgraph Ingestion ["📥 Document Ingestion (Offline)"]
        Docs[📄 Raw Documents\nPDF / MD / HTML]
        Loader[Document Loader]
        Splitter[Text Splitter\nChunk: 512 tokens\nOverlap: 50 tokens]
        Embedder1[🔢 Embedding Model\ntext-embedding-3-small]
        VecStore[("🗄️ Vector Store\nPinecone / pgvector")]

        Docs --> Loader --> Splitter --> Embedder1 --> VecStore
    end

    subgraph Query ["💬 Query Path (Online)"]
        User[👤 User]
        QueryEmbed[🔢 Embed Query\ntext-embedding-3-small]
        Retriever[🔍 Retriever\nTop-K Similarity Search]
        Reranker[⚖️ Re-ranker\nCross-Encoder]
        CtxBuilder[📝 Context Builder\nPrompt Template]
        LLM[🧠 LLM\nGPT-4o / Claude 3.5]
        Answer[💡 Answer]

        User --> QueryEmbed --> Retriever
        VecStore --> Retriever
        Retriever --> Reranker --> CtxBuilder --> LLM --> Answer
    end

    subgraph Observability ["📊 Observability"]
        Langfuse[📈 Langfuse\nTrace & Evals]
        LLM -.->|traces| Langfuse
    end
```

**Key design decisions:**
- **Chunking overlap** prevents context from being split at sentence boundaries
- **Re-ranker** reduces hallucination by filtering noisy top-K results to top-3
- **Separate embedding pipelines** for ingestion vs. query allows model upgrades without re-querying

---

## 2. LLM API Gateway (Multi-Model Routing)

Route requests to the cheapest/fastest model that can handle the task.

```mermaid
flowchart LR
    subgraph Clients
        WebApp[🌐 Web App]
        MobileApp[📱 Mobile App]
        Internal[⚙️ Internal Services]
    end

    subgraph Gateway ["🚦 LLM API Gateway"]
        Auth[🔐 Auth &\nRate Limiter]
        Router[🧭 Smart Router\nRoute by task type,\ncost, latency SLA]
        Cache[⚡ Semantic Cache\nRedis + embeddings]
        Logger[📝 Request Logger]
    end

    subgraph Models ["🧠 Model Pool"]
        Fast[⚡ Fast Model\nGPT-4o-mini\n~$0.15/1M tokens]
        Power[💪 Power Model\nGPT-4o / Claude 3.5\n~$5/1M tokens]
        Local[🏠 Local Model\nLlama 3 / Mistral\n$0 — on-prem]
        Vision[👁️ Vision Model\nGPT-4o Vision\nfor image tasks]
    end

    subgraph Observability ["📊 Monitoring"]
        Metrics[📈 Cost & Latency\nMetrics]
        Alerts[🔔 Budget Alerts]
    end

    WebApp & MobileApp & Internal --> Auth
    Auth --> Cache
    Cache -- Cache Hit --> Clients
    Cache -- Cache Miss --> Router
    Router -->|simple Q&A| Fast
    Router -->|reasoning tasks| Power
    Router -->|private data| Local
    Router -->|image input| Vision
    Fast & Power & Local & Vision --> Logger
    Logger --> Metrics --> Alerts
```

**Cost optimization levers:**
- Semantic cache: identical or near-identical questions skip the model entirely
- Smart routing: 60-70% of requests go to the cheap fast model
- Local model: GDPR-sensitive data never leaves your infra

---

## 3. MLOps Training Pipeline

From raw data to a deployed, monitored model.

```mermaid
flowchart TD
    subgraph DataLayer ["🗃️ Data Layer"]
        RawData[Raw Data\nS3 / GCS Bucket]
        FeatureStore[("🏪 Feature Store\nFeast / Tecton")]
        DataValid[✅ Data Validation\nGreat Expectations]
    end

    subgraph Training ["🏋️ Training Pipeline (Kubeflow)"]
        Preprocess[⚙️ Preprocessing\nNormalize, encode, split]
        Train[🧠 Model Training\nPyTorch / XGBoost]
        Eval[📊 Evaluation\nF1 / AUC / RMSE]
        Promote{Metric threshold\nmet?}
    end

    subgraph Registry ["📦 Model Registry"]
        MLflow[("MLflow\nModel Registry")]
        Staging[🟡 Staging]
        Production[🟢 Production]
    end

    subgraph Serving ["🚀 Serving"]
        Triton[Triton Inference\nServer]
        ShadowDeploy[🌑 Shadow Deployment\nA/B Traffic Split]
        Monitor[📉 Drift Monitor\nEvidentlyAI]
    end

    RawData --> DataValid --> FeatureStore
    FeatureStore --> Preprocess --> Train --> Eval --> Promote
    Promote -- No --> Train
    Promote -- Yes --> MLflow
    MLflow --> Staging --> Production
    Production --> Triton --> ShadowDeploy
    ShadowDeploy --> Monitor
    Monitor -- Drift detected --> Train
```

---

## 4. AI Agent with Tool Use (ReAct Pattern)

A single-agent loop that plans, calls tools, and synthesises results.

```mermaid
sequenceDiagram
    actor User
    participant Agent as 🧠 AI Agent<br/>(GPT-4o)
    participant Planner as 📋 Task Planner
    participant Tools as 🛠️ Tool Registry
    participant Web as 🌐 Web Search
    participant Code as 💻 Code Interpreter
    participant DB as 🗄️ Database
    participant Memory as 💾 Memory Store

    User->>Agent: "Analyse our Q1 sales and compare to industry benchmarks"
    Agent->>Memory: Load conversation context
    Memory-->>Agent: Previous context
    Agent->>Planner: Decompose task into sub-tasks
    Planner-->>Agent: [1] Query DB for Q1 data, [2] Search benchmarks, [3] Analyse & compare

    loop ReAct Loop
        Agent->>Tools: Select tool for next step
        alt DB Query
            Tools->>DB: SELECT revenue, region FROM sales WHERE quarter='Q1'
            DB-->>Tools: ResultSet
        else Web Search
            Tools->>Web: "SaaS industry Q1 2025 revenue benchmarks"
            Web-->>Tools: Search results
        else Code Interpreter
            Tools->>Code: Run analysis script (pandas + matplotlib)
            Code-->>Tools: Chart + summary stats
        end
        Tools-->>Agent: Tool result
        Agent->>Memory: Store intermediate result
    end

    Agent->>User: Final analysis with chart + recommendations
```
