# ☁️ Cloud Infrastructure Examples

Four production cloud architecture diagrams with complete Mermaid code.

---

## 1. AWS Three-Tier Web Application

Classic scalable web app: CDN → ALB → ECS → RDS.

```mermaid
flowchart TD
    User[👤 User] --> CF

    subgraph Edge ["🌍 Edge / DNS"]
        R53[🌐 Route 53\nGeoDNS + Health Checks]
        CF[☁️ CloudFront CDN\nEdge Caching]
    end

    subgraph PublicSubnet ["🌐 Public Subnet (AZ-a & AZ-b)"]
        ALB[⚖️ Application\nLoad Balancer]
        NAT[🔁 NAT Gateway]
    end

    subgraph PrivateSubnet ["🔒 Private Subnet (AZ-a & AZ-b)"]
        subgraph ECS ["ECS Fargate Cluster"]
            Task1[🟢 Task: API\nNode.js]
            Task2[🟢 Task: API\nNode.js]
            Task3[🟢 Task: Worker\nBackground Jobs]
        end
    end

    subgraph DataSubnet ["🗄️ Data Subnet (AZ-a & AZ-b)"]
        RDS[("🐘 RDS Aurora\nPostgreSQL\nMulti-AZ")]
        Elasticache[("⚡ ElastiCache\nRedis Cluster\n(sessions & cache)")]
    end

    subgraph Services ["☁️ AWS Managed Services"]
        SQS[📨 SQS Queue\nJob Queue]
        S3[🗃️ S3 Bucket\nStatic Assets & Uploads]
        SecretsManager[🔐 Secrets Manager]
        CloudWatch[📊 CloudWatch\nLogs & Metrics]
    end

    CF --> R53
    R53 --> ALB
    ALB --> Task1 & Task2
    Task1 & Task2 --> RDS
    Task1 & Task2 --> Elasticache
    Task1 & Task2 --> SQS
    SQS --> Task3
    Task3 --> RDS
    Task3 --> S3
    Task1 & Task2 --> SecretsManager
    ECS --> CloudWatch
    NAT --> Internet([🌐 Internet])
    PrivateSubnet --> NAT
```

**Key AWS services explained:**
- **CloudFront** caches static assets at edge; reduces ALB load by ~40%
- **ECS Fargate** — no EC2 to manage; scales containers automatically
- **Aurora Multi-AZ** — synchronous standby replica; sub-30s failover
- **ElastiCache** stores sessions so any task can serve any user

---

## 2. Kubernetes Cluster Architecture

Production k8s cluster on EKS/GKE with namespaced workloads.

```mermaid
flowchart TD
    Internet[🌐 Internet] --> Ingress

    subgraph ControlPlane ["☸️ Control Plane (Managed)"]
        APIServer[API Server]
        ETCD[("etcd")]
        Scheduler[Scheduler]
        CM[Controller Manager]
        APIServer <--> ETCD
        APIServer --> Scheduler & CM
    end

    subgraph WorkerNodes ["🖥️ Worker Node Pool"]
        subgraph NS_Prod ["Namespace: production"]
            Ingress[🚦 Ingress Controller\nnginx / ALB]
            subgraph Deployments
                FE[Frontend\nReact — 3 pods]
                BE[Backend API\nNode.js — 5 pods]
                Worker[Worker\nPython — 2 pods]
            end
            HPA[📈 HPA\nAuto-scales pods]
        end

        subgraph NS_Infra ["Namespace: infra"]
            Prom[📊 Prometheus]
            Grafana[📉 Grafana]
            Loki[📜 Loki\nLog aggregation]
        end

        subgraph NS_Data ["Namespace: data"]
            PG[("🐘 PostgreSQL\nStatefulSet")]
            Redis[("⚡ Redis\nStatefulSet")]
        end
    end

    subgraph ExternalSecrets ["🔐 External Secrets"]
        Vault[HashiCorp Vault\nor AWS Secrets Manager]
    end

    Internet --> Ingress
    Ingress --> FE & BE
    BE --> Worker
    BE --> PG & Redis
    Worker --> PG
    BE & Worker -.->|metrics| Prom
    Prom --> Grafana
    BE & Worker -.->|logs| Loki
    WorkerNodes -.->|secret sync| Vault
    ControlPlane --> WorkerNodes
    HPA -.-> Deployments
```

---

## 3. Multi-Cloud / Hybrid Architecture

Primary on AWS, disaster recovery on GCP, on-prem for sensitive data.

```mermaid
flowchart LR
    subgraph OnPrem ["🏢 On-Premises (Private Data)"]
        CoreDB[("🔒 Core Database\nPatient / PII data")]
        IDProvider[🔑 Identity Provider\nActive Directory]
        PrivateAPI[🔗 Internal API\ngRPC]
    end

    subgraph AWS ["☁️ AWS (Primary)"]
        AWSVPN[🔗 AWS VPN /\nDirect Connect]
        EKS[☸️ EKS Cluster\nApp Workloads]
        S3Primary[🗃️ S3\nPrimary Storage]
        RDSPrimary[("🐘 RDS\nApp Database")]
    end

    subgraph GCP ["☁️ GCP (DR / Analytics)"]
        GCPVPN[🔗 Cloud VPN]
        GKE[☸️ GKE Cluster\nDR Standby]
        BigQuery[("📊 BigQuery\nAnalytics")]
        GCSBackup[🗃️ GCS\nBackup Storage]
    end

    subgraph CDN ["🌍 Global Edge"]
        CloudflareGlobal[☁️ Cloudflare\nGlobal Load Balancer\n+ WAF + DDoS]
    end

    Users[👤 Global Users] --> CloudflareGlobal
    CloudflareGlobal -->|primary traffic| EKS
    CloudflareGlobal -->|failover| GKE

    EKS <-->|Direct Connect| AWSVPN
    AWSVPN <-->|VPN Tunnel| OnPrem
    GCPVPN <-->|VPN Tunnel| OnPrem

    EKS --> RDSPrimary
    EKS --> S3Primary
    S3Primary -->|cross-cloud replication| GCSBackup
    RDSPrimary -.->|binlog replication| GKE

    CoreDB <--> PrivateAPI
    PrivateAPI <--> EKS
    IDProvider -->|SSO / SAML| EKS & GKE

    S3Primary -->|ETL pipeline| BigQuery
```

---

## 4. CI/CD to Cloud Deployment Pipeline

From `git push` to production on Kubernetes.

```mermaid
flowchart TD
    Dev[👩‍💻 Developer] -->|git push| GitHub[🐙 GitHub\nfeature branch]
    GitHub -->|PR opened| CI

    subgraph CI ["⚙️ CI — GitHub Actions"]
        Lint[🔍 Lint & Type Check]
        UnitTest[🧪 Unit Tests]
        IntTest[🔬 Integration Tests]
        SAST[🔐 SAST Scan\nCodeQL / Snyk]
        Build[🏗️ Docker Build]
        Push[📦 Push to ECR\n:sha-abc1234]

        Lint --> UnitTest --> IntTest --> SAST --> Build --> Push
    end

    Push -->|PR merged to main| CD

    subgraph CD ["🚀 CD — ArgoCD (GitOps)"]
        UpdateManifest[✏️ Update image tag\nin k8s manifests repo]
        ArgoCDSync[🔄 ArgoCD detects drift\nauto-syncs cluster]
        RolloutStaging[🟡 Deploy to Staging\nCanary: 10%→50%→100%]
        SmokeTest[💨 Smoke Tests\nPost-deploy health checks]
        ApprovalGate{✅ Manual\nApproval}
        RolloutProd[🟢 Deploy to Production\nBlue-Green rollout]
        Rollback[⏪ Auto Rollback\nif error rate > 1%]
    end

    subgraph Observability ["📊 Observability"]
        Datadog[📈 Datadog\nAPM + Dashboards]
        PagerDuty[🚨 PagerDuty\nOn-call Alerts]
    end

    CD --> UpdateManifest --> ArgoCDSync --> RolloutStaging
    RolloutStaging --> SmokeTest --> ApprovalGate
    ApprovalGate -- Approved --> RolloutProd
    ApprovalGate -- Rejected --> Rollback
    RolloutProd --> Datadog
    Datadog -->|alert on SLO breach| PagerDuty
    RolloutProd -->|error spike| Rollback
```
