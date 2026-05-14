# ☁️ Cloud Infrastructure Template

Two fill-in-the-blank templates for common cloud patterns.

Replace every `[PLACEHOLDER]` with your real component names.

---

## Template 1: Single-Cloud Three-Tier App

```mermaid
flowchart TD
    User["👤 [USER_TYPE]\ne.g. Web / Mobile / API consumer"]

    subgraph Edge ["🌍 Edge & DNS"]
        DNS["[DNS_SERVICE]\ne.g. Route 53 / Cloud DNS / Cloudflare"]
        CDN["[CDN_SERVICE]\ne.g. CloudFront / Fastly / Akamai"]
    end

    subgraph LB ["⚖️ Load Balancer"]
        ALB["[LOAD_BALANCER]\ne.g. AWS ALB / GCP Load Balancer / nginx"]
    end

    subgraph App ["🟢 Application Layer\n[COMPUTE_PLATFORM]  e.g. ECS Fargate / GKE / App Engine"]
        Svc1["[SERVICE_1]\ne.g. API Server\n[RUNTIME]"]
        Svc2["[SERVICE_2]\ne.g. Background Worker\n[RUNTIME]"]
    end

    subgraph Data ["🗄️ Data Layer"]
        PrimaryDB[("🐘 [PRIMARY_DB]\ne.g. RDS PostgreSQL / Cloud SQL / Aurora")]
        Cache[("⚡ [CACHE]\ne.g. ElastiCache Redis / Memorystore")]
        Queue["[QUEUE]\ne.g. SQS / Cloud Tasks / RabbitMQ"]
    end

    subgraph Storage ["🗃️ Object Storage"]
        ObjStore["[OBJECT_STORE]\ne.g. S3 / GCS / Azure Blob"]
    end

    subgraph Observability ["📊 Observability"]
        Logs["[LOG_PLATFORM]\ne.g. CloudWatch / Datadog / Loki"]
        Metrics["[METRICS_PLATFORM]\ne.g. Prometheus / Grafana / Datadog"]
    end

    User --> DNS --> CDN --> ALB
    ALB --> Svc1
    Svc1 --> PrimaryDB & Cache & Queue
    Queue --> Svc2
    Svc2 --> PrimaryDB & ObjStore
    Svc1 & Svc2 -.->|logs & metrics| Logs & Metrics
```

**Placeholders to fill:**
- `[USER_TYPE]` — who or what calls this system
- `[DNS_SERVICE]` — your DNS provider
- `[CDN_SERVICE]` — edge caching / DDoS protection
- `[LOAD_BALANCER]` — L7 load balancer
- `[COMPUTE_PLATFORM]` — how containers / VMs are managed
- `[SERVICE_1/2]` — name your app services
- `[RUNTIME]` — language/framework (Node.js, Python, Go…)
- `[PRIMARY_DB]` — main transactional database
- `[CACHE]` — in-memory cache for hot data
- `[QUEUE]` — async job queue
- `[OBJECT_STORE]` — blob / file storage
- `[LOG_PLATFORM]` / `[METRICS_PLATFORM]` — observability stack

---

## Template 2: Kubernetes Cluster

```mermaid
flowchart TD
    Internet["🌐 Internet"] --> Ingress

    subgraph Cluster ["☸️ [CLUSTER_NAME]  ([CLOUD_PROVIDER] [K8S_SERVICE])"]

        subgraph NS_App ["Namespace: [APP_NAMESPACE]"]
            Ingress["🚦 Ingress Controller\n[INGRESS_TYPE]  e.g. nginx / ALB ingress"]
            FE["[FRONTEND_SERVICE]\n[N] replicas — [FRAMEWORK]"]
            BE["[BACKEND_SERVICE]\n[N] replicas — [FRAMEWORK]"]
            Workers["[WORKER_SERVICE]\n[N] replicas"]
            HPA["📈 HPA\nscale on: [HPA_METRIC]"]
        end

        subgraph NS_Data ["Namespace: [DATA_NAMESPACE]"]
            DB[("🐘 [DB_SERVICE]\nStatefulSet — [DB_TYPE]")]
            Redis[("⚡ [CACHE_SERVICE]\nStatefulSet — Redis")]
        end

        subgraph NS_Ops ["Namespace: [OPS_NAMESPACE]"]
            Prom["📊 Prometheus"]
            Grafana["📉 Grafana\n[DASHBOARD_NAME] dashboard"]
            Loki["📜 Loki"]
        end
    end

    subgraph Secrets ["🔐 Secrets Backend"]
        SecretStore["[SECRET_BACKEND]\ne.g. AWS Secrets Manager / Vault / GCP Secret Manager"]
    end

    Internet --> Ingress --> FE & BE
    BE --> DB & Redis
    BE --> Workers
    Workers --> DB
    BE & Workers -.->|metrics| Prom
    Prom --> Grafana
    BE & Workers -.->|logs| Loki
    NS_App -.->|ESO sync| SecretStore
    HPA -.-> FE & BE & Workers
```

**Placeholders to fill:**
- `[CLUSTER_NAME]` — name of your k8s cluster
- `[CLOUD_PROVIDER]` / `[K8S_SERVICE]` — e.g. AWS EKS, GKE, AKS
- `[APP_NAMESPACE]` / `[DATA_NAMESPACE]` / `[OPS_NAMESPACE]` — your namespace names
- `[INGRESS_TYPE]` — nginx, AWS ALB ingress controller, etc.
- `[FRONTEND/BACKEND/WORKER_SERVICE]` — your service names
- `[N]` — replica count per service
- `[FRAMEWORK]` — tech stack per service
- `[HPA_METRIC]` — CPU / memory / custom metric
- `[DB_SERVICE]` / `[DB_TYPE]` — e.g. postgres / mysql / mongodb
- `[CACHE_SERVICE]` — your Redis service name
- `[DASHBOARD_NAME]` — Grafana dashboard name
- `[SECRET_BACKEND]` — external secrets operator backend
