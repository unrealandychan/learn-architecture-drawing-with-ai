# 🤖 AI / ML Pipeline Template

Three fill-in-the-blank templates for common AI system patterns.

Replace every `[PLACEHOLDER]` with your real component names.

---

## Template 1: RAG Chatbot

```mermaid
flowchart TD
    subgraph Ingestion ["📥 Document Ingestion (Offline)"]
        Src["📄 [DATA SOURCE]\ne.g. PDFs, Confluence, Notion"]
        Loader["Document Loader"]
        Splitter["Text Splitter\nChunk: [CHUNK_SIZE] tokens\nOverlap: [OVERLAP] tokens"]
        Embedder["🔢 Embedding Model\n[EMBEDDING_MODEL]\ne.g. text-embedding-3-small"]
        VecDB[("🗄️ Vector Store\n[VECTOR_DB]\ne.g. Pinecone / pgvector / Weaviate")]

        Src --> Loader --> Splitter --> Embedder --> VecDB
    end

    subgraph QueryPath ["💬 Query Path (Online)"]
        User["👤 User"]
        QEmbed["🔢 Embed Query\n[EMBEDDING_MODEL]"]
        Retriever["🔍 Retriever\nTop-[K] results"]
        Reranker["⚖️ Re-ranker\n[RERANKER_MODEL]\ne.g. cohere-rerank / cross-encoder"]
        CtxBuilder["📝 Prompt Builder\n[PROMPT_TEMPLATE]"]
        LLM["🧠 LLM\n[LLM_MODEL]\ne.g. GPT-4o / Claude 3.5 / Llama 3"]
        Answer["💡 Response to User"]

        User --> QEmbed --> Retriever
        VecDB --> Retriever
        Retriever --> Reranker --> CtxBuilder --> LLM --> Answer
    end

    subgraph Eval ["📊 Evals & Observability (Optional)"]
        Tracer["[OBSERVABILITY_TOOL]\ne.g. Langfuse / LangSmith / Arize"]
        LLM -.->|trace| Tracer
    end
```

**Placeholders to fill:**
- `[DATA SOURCE]` — where your documents live (S3, Notion, file system)
- `[CHUNK_SIZE]` / `[OVERLAP]` — typical starting point: 512 / 50
- `[EMBEDDING_MODEL]` — must match between ingestion and query time
- `[VECTOR_DB]` — Pinecone (managed), pgvector (self-hosted), Weaviate (open source)
- `[K]` — number of chunks to retrieve (typical: 5–20)
- `[RERANKER_MODEL]` — optional but improves precision
- `[LLM_MODEL]` — your generation model
- `[PROMPT_TEMPLATE]` — name of your system prompt / template
- `[OBSERVABILITY_TOOL]` — tracing & eval platform

---

## Template 2: ML Training & Serving Pipeline

```mermaid
flowchart TD
    subgraph Data ["🗃️ Data"]
        Raw["Raw Data\n[DATA_STORE]\ne.g. S3 / GCS / Snowflake"]
        Valid["Data Validation\n[VALIDATION_TOOL]\ne.g. Great Expectations"]
        FStore[("Feature Store\n[FEATURE_STORE]\ne.g. Feast / Tecton / Hopsworks")]
    end

    subgraph Training ["🏋️ Training\n[TRAINING_PLATFORM]  e.g. Kubeflow / SageMaker / Vertex AI"]
        Preprocess["⚙️ Preprocessing\nnormalize, encode, split"]
        Train["🧠 Train Model\n[FRAMEWORK]\ne.g. PyTorch / XGBoost / Keras"]
        Eval["📊 Evaluate\n[METRIC]: [THRESHOLD]\ne.g. F1 ≥ 0.90"]
        Gate{Threshold\nmet?}
    end

    subgraph Registry ["📦 Model Registry\n[MODEL_REGISTRY]  e.g. MLflow / W&B / Vertex Model Registry"]
        Staging["🟡 Staging"]
        Prod["🟢 Production"]
    end

    subgraph Serving ["🚀 Serving\n[SERVING_PLATFORM]  e.g. Triton / BentoML / TorchServe"]
        Endpoint["REST / gRPC Endpoint"]
        Monitor["📉 Drift Monitor\n[DRIFT_TOOL]\ne.g. EvidentlyAI / Arize"]
    end

    Raw --> Valid --> FStore
    FStore --> Preprocess --> Train --> Eval --> Gate
    Gate -- No --> Train
    Gate -- Yes --> Staging --> Prod
    Prod --> Endpoint --> Monitor
    Monitor -- Drift detected --> Train
```

**Placeholders to fill:**
- `[DATA_STORE]` — where raw data lives
- `[VALIDATION_TOOL]` — data quality checker
- `[FEATURE_STORE]` — centralised feature computation layer
- `[TRAINING_PLATFORM]` — orchestration for training jobs
- `[FRAMEWORK]` — ML training library
- `[METRIC]` / `[THRESHOLD]` — your promotion gate (e.g. AUC ≥ 0.92)
- `[MODEL_REGISTRY]` — model versioning store
- `[SERVING_PLATFORM]` — inference server
- `[DRIFT_TOOL]` — model monitoring tool

---

## Template 3: AI Agent with Tools

```mermaid
sequenceDiagram
    actor User
    participant Agent as 🧠 Agent<br/>[LLM_MODEL]
    participant Memory as 💾 [MEMORY_STORE]<br/>e.g. Redis / Zep
    participant Tool1 as 🛠️ [TOOL_1]<br/>e.g. Web Search
    participant Tool2 as 🛠️ [TOOL_2]<br/>e.g. Code Interpreter
    participant Tool3 as 🛠️ [TOOL_3]<br/>e.g. Database

    User->>Agent: [EXAMPLE_USER_REQUEST]
    Agent->>Memory: Load context
    Memory-->>Agent: Previous turns

    loop ReAct Loop (Plan → Act → Observe)
        Agent->>Agent: Think: which tool next?
        alt [TOOL_1_CONDITION]
            Agent->>Tool1: [TOOL_1_INPUT]
            Tool1-->>Agent: [TOOL_1_OUTPUT]
        else [TOOL_2_CONDITION]
            Agent->>Tool2: [TOOL_2_INPUT]
            Tool2-->>Agent: [TOOL_2_OUTPUT]
        else [TOOL_3_CONDITION]
            Agent->>Tool3: [TOOL_3_INPUT]
            Tool3-->>Agent: [TOOL_3_OUTPUT]
        end
        Agent->>Memory: Store result
    end

    Agent->>User: Final answer
```

**Placeholders to fill:**
- `[LLM_MODEL]` — e.g. GPT-4o, Claude 3.5 Sonnet
- `[MEMORY_STORE]` — short-term or long-term memory backend
- `[TOOL_1/2/3]` — name each tool the agent can call
- `[TOOL_N_CONDITION]` — when does the agent pick this tool?
- `[TOOL_N_INPUT/OUTPUT]` — example inputs and what the tool returns
- `[EXAMPLE_USER_REQUEST]` — a representative task this agent handles
