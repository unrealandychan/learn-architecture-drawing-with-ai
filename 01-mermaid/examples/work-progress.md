# 📅 Work Progress Examples

Four diagrams for tracking project status, sprint work, team workflows, and release timelines.

---

## 1. Sprint Board (Kanban-style)

Visual snapshot of a two-week sprint — great for standups and stakeholder updates.

```mermaid
---
config:
  kanban:
    ticketBaseUrl: 'https://jira.example.com/browse/#TICKET#'
---
kanban
  column0["📥 Backlog"]
    task_0["PROJ-45\nRedesign onboarding flow"]
    task_1["PROJ-46\nAdd SSO via SAML"]

  column1["🔵 In Progress"]
    task_2["PROJ-41\nAPI rate limiting"]@{ ticket: PROJ-41, assigned: 'alice' }
    task_3["PROJ-42\nDashboard charts v2"]@{ ticket: PROJ-42, assigned: 'bob' }
    task_4["PROJ-43\nDB migration script"]@{ ticket: PROJ-43, assigned: 'carol' }

  column2["🔍 In Review"]
    task_5["PROJ-38\nEmail notification service"]@{ ticket: PROJ-38, assigned: 'alice' }
    task_6["PROJ-39\nSearch endpoint pagination"]@{ ticket: PROJ-39, assigned: 'dave' }

  column3["🧪 QA / Testing"]
    task_7["PROJ-35\nMobile responsive layout"]@{ ticket: PROJ-35, assigned: 'QA' }

  column4["✅ Done"]
    task_8["PROJ-31\nUser profile settings"]
    task_9["PROJ-32\nPassword reset flow"]
    task_10["PROJ-33\nCI pipeline speed-up"]
    task_11["PROJ-34\nError tracking (Sentry)"]
```

> **Tip:** If your Mermaid renderer doesn't support `kanban` yet, use the flowchart version below.

### Flowchart alternative (universal support)

```mermaid
flowchart LR
    subgraph Backlog["📥 Backlog"]
        B1["PROJ-45\nRedesign onboarding"]
        B2["PROJ-46\nAdd SSO / SAML"]
    end

    subgraph InProgress["🔵 In Progress"]
        P1["PROJ-41\n🔴 API rate limiting\n— Alice"]
        P2["PROJ-42\n🟡 Dashboard charts v2\n— Bob"]
        P3["PROJ-43\n🟡 DB migration\n— Carol"]
    end

    subgraph Review["🔍 In Review"]
        R1["PROJ-38\nEmail notifications\n— Alice"]
        R2["PROJ-39\nSearch pagination\n— Dave"]
    end

    subgraph QA["🧪 QA"]
        Q1["PROJ-35\nMobile layout\n— QA Team"]
    end

    subgraph Done["✅ Done"]
        D1["PROJ-31 ✓\nProfile settings"]
        D2["PROJ-32 ✓\nPassword reset"]
        D3["PROJ-33 ✓\nCI speed-up"]
    end
```

---

## 2. Project Timeline (Gantt Chart)

Quarter-view roadmap for a product launch — useful for execs and PMs.

```mermaid
gantt
    title Product Launch — Q2 / Q3 2025
    dateFormat  YYYY-MM-DD
    axisFormat  %b %d

    section 🔍 Discovery
    Stakeholder interviews         :done,    disc1, 2025-04-01, 2025-04-07
    Requirements doc               :done,    disc2, 2025-04-07, 2025-04-14
    Tech spike — AI features       :done,    disc3, 2025-04-10, 2025-04-18

    section 🏗️ Backend
    Auth service                   :done,    be1,   2025-04-15, 2025-04-28
    API v1 (CRUD + search)         :done,    be2,   2025-04-22, 2025-05-09
    Payments integration           :active,  be3,   2025-05-05, 2025-05-23
    AI recommendation engine       :         be4,   2025-05-19, 2025-06-06

    section 🎨 Frontend
    Design system setup            :done,    fe1,   2025-04-15, 2025-04-25
    Onboarding flow                :done,    fe2,   2025-04-25, 2025-05-09
    Dashboard v1                   :active,  fe3,   2025-05-07, 2025-05-27
    Mobile responsive polish       :         fe4,   2025-05-26, 2025-06-06

    section 🚀 Launch
    Internal beta (team)           :milestone, m1,  2025-05-30, 1d
    Closed beta (100 users)        :         launch1, 2025-06-02, 2025-06-16
    Public launch                  :milestone, m2,  2025-06-16, 1d
    Post-launch monitoring         :         launch2, 2025-06-16, 2025-06-30
```

---

## 3. Team Incident Response Workflow

Who does what when production goes down — from alert to post-mortem.

```mermaid
sequenceDiagram
    participant Monitor as 📊 Monitoring<br/>(Datadog)
    participant PD as 🚨 PagerDuty
    participant OnCall as 🧑‍💻 On-Call Eng
    participant Lead as 👩‍💼 Tech Lead
    participant Team as 👥 Response Team
    participant Comms as 📢 Comms Lead
    participant PM as 🗃️ Post-Mortem

    Monitor->>PD: SLO breach — error rate > 5%
    PD->>OnCall: Page (P1 alert)
    OnCall->>OnCall: Acknowledge (< 5 min SLA)
    OnCall->>Lead: Escalate if not resolved in 15 min

    Lead->>Team: Open incident bridge call
    Team->>Team: Investigate logs, traces, dashboards

    alt Root cause found
        Team->>Team: Apply fix / rollback
        Team->>Monitor: Confirm metrics recovering
        Monitor-->>Team: ✅ Error rate normal
    else Cannot find root cause
        Team->>Lead: Escalate to P0
        Lead->>Comms: Trigger customer communication
        Comms->>Comms: Post status page update
    end

    OnCall->>PD: Resolve incident
    PD->>Team: Incident closed — MTTR recorded

    Note over Team, PM: 48h later
    Team->>PM: Draft post-mortem document
    PM->>PM: 5-Whys root cause analysis
    PM->>Team: Review & action items assigned
```

---

## 4. Feature Delivery Workflow (PR to Production)

From ticket creation to deployed feature — end to end.

```mermaid
flowchart TD
    Ticket["🎫 Jira Ticket\nAC written, estimate added"]
    Branch["🌿 git checkout -b\nfeature/PROJ-42"]
    Dev["💻 Development\nCode + unit tests"]
    PR["📬 Pull Request\nself-review checklist ✓"]

    subgraph Review ["👀 Code Review"]
        Reviewer1["Peer Review\n(1 approval required)"]
        Reviewer2["Tech Lead Review\n(for arch changes)"]
    end

    subgraph CI ["⚙️ CI Checks"]
        Tests["🧪 Tests pass"]
        Coverage["📊 Coverage ≥ 80%"]
        Lint["🔍 Lint clean"]
        SecScan["🔐 Security scan"]
    end

    Merge["🟢 Merge to main"]
    Staging["🟡 Auto-deploy\nto Staging"]
    QATest["🧪 QA Verification\n(happy path + edge cases)"]
    ProdDeploy["🚀 Deploy to Production\n(blue-green / canary)"]
    Monitor["📈 Monitor 30 min\n(error rate, latency)"]
    TicketDone["✅ Ticket → Done\nRelease notes updated"]

    Ticket --> Branch --> Dev --> PR
    PR --> Reviewer1 & Reviewer2
    Reviewer1 & Reviewer2 --> CI
    Tests & Coverage & Lint & SecScan --> Merge
    Merge --> Staging --> QATest
    QATest -- Pass --> ProdDeploy
    QATest -- Fail --> Dev
    ProdDeploy --> Monitor
    Monitor -- Stable --> TicketDone
    Monitor -- Issue found --> Merge
```
