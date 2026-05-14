# 📅 Work Progress Template

Two fill-in-the-blank templates for project tracking and team workflows.

Replace every `[PLACEHOLDER]` with your real values.

---

## Template 1: Project Gantt / Roadmap

```mermaid
gantt
    title [PROJECT_NAME] — [QUARTER / TIMEFRAME]
    dateFormat  YYYY-MM-DD
    axisFormat  %b %d

    section 🔍 [PHASE_1_NAME]
    [TASK_1_1]    :done,    t11, [START_DATE], [END_DATE]
    [TASK_1_2]    :done,    t12, [START_DATE], [END_DATE]
    [TASK_1_3]    :active,  t13, [START_DATE], [END_DATE]

    section 🏗️ [PHASE_2_NAME]
    [TASK_2_1]    :done,    t21, [START_DATE], [END_DATE]
    [TASK_2_2]    :active,  t22, [START_DATE], [END_DATE]
    [TASK_2_3]    :         t23, [START_DATE], [END_DATE]
    [TASK_2_4]    :         t24, [START_DATE], [END_DATE]

    section 🎨 [PHASE_3_NAME]
    [TASK_3_1]    :done,    t31, [START_DATE], [END_DATE]
    [TASK_3_2]    :active,  t32, [START_DATE], [END_DATE]
    [TASK_3_3]    :         t33, [START_DATE], [END_DATE]

    section 🚀 [PHASE_4_NAME]
    [MILESTONE_1] :milestone, m1, [MILESTONE_DATE], 1d
    [TASK_4_1]    :         t41, [START_DATE], [END_DATE]
    [TASK_4_2]    :         t42, [START_DATE], [END_DATE]
    [MILESTONE_2] :milestone, m2, [MILESTONE_DATE], 1d
```

**Status codes:**
- `:done,` — completed task (shown in dark)
- `:active,` — in-progress task (shown highlighted)
- `:crit,` — on the critical path (shown in red)
- `:milestone,` — key date marker

**Placeholders to fill:**
- `[PROJECT_NAME]` — e.g. "Mobile App Relaunch"
- `[QUARTER / TIMEFRAME]` — e.g. "Q2 2025"
- `[PHASE_1/2/3/4_NAME]` — e.g. Discovery, Backend, Frontend, Launch
- `[TASK_N_N]` — task label shown on the Gantt bar
- `[START_DATE]` / `[END_DATE]` — ISO format: `2025-06-01`
- `[MILESTONE_1/2]` — key delivery or go/no-go decision point
- `[MILESTONE_DATE]` — single date for the milestone marker

---

## Template 2: Feature Delivery Workflow

```mermaid
flowchart TD
    Ticket["🎫 [TICKET_SYSTEM] Ticket\n[TICKET_ID]: [TICKET_TITLE]"]
    Branch["🌿 git checkout -b\n[BRANCH_NAMING_CONVENTION]\ne.g. feature/[TICKET_ID]"]
    Dev["💻 Development\n[DEV_CHECKLIST]"]
    PR["📬 Pull Request\n[PR_TEMPLATE_CHECKLIST]"]

    subgraph Review ["👀 Code Review"]
        Peer["Peer Review\n([APPROVAL_COUNT] approval required)"]
        Lead["[SENIOR_ROLE] Review\n(required for [REVIEW_TRIGGER])"]
    end

    subgraph CI ["⚙️ CI — [CI_TOOL]"]
        Tests["🧪 [TEST_SUITE] pass"]
        Coverage["📊 Coverage ≥ [COVERAGE_THRESHOLD]%"]
        Lint["🔍 [LINT_TOOL] clean"]
        SecScan["🔐 [SECURITY_SCANNER] scan"]
    end

    Merge["🟢 Merge to [BASE_BRANCH]"]

    subgraph Deploy ["🚀 Deploy — [CD_TOOL]"]
        Staging["🟡 [STAGING_ENV]\n[STAGING_DEPLOY_STRATEGY]"]
        QA["🧪 QA Verification\n[QA_CHECKLIST]"]
        Approval{[APPROVAL_ROLE]\nApproval}
        Prod["🟢 [PROD_ENV]\n[PROD_DEPLOY_STRATEGY]"]
    end

    Monitor["📈 Monitor [MONITOR_DURATION]\n[MONITOR_METRICS]"]
    Done["✅ [TICKET_SYSTEM] → Done\n[RELEASE_PROCESS]"]

    Ticket --> Branch --> Dev --> PR
    PR --> Peer & Lead
    Peer & Lead --> CI
    Tests & Coverage & Lint & SecScan --> Merge
    Merge --> Staging --> QA
    QA -- Pass --> Approval
    QA -- Fail --> Dev
    Approval -- Approved --> Prod
    Approval -- Rejected --> Dev
    Prod --> Monitor
    Monitor -- Stable --> Done
    Monitor -- Issue --> Merge
```

**Placeholders to fill:**
- `[TICKET_SYSTEM]` — Jira / Linear / GitHub Issues
- `[TICKET_ID]` / `[TICKET_TITLE]` — e.g. PROJ-42 / "Add payment integration"
- `[BRANCH_NAMING_CONVENTION]` — e.g. `feature/PROJ-42`, `feat/short-name`
- `[DEV_CHECKLIST]` — e.g. "Unit tests written, types added"
- `[PR_TEMPLATE_CHECKLIST]` — items from your PR template
- `[APPROVAL_COUNT]` — 1 or 2
- `[SENIOR_ROLE]` — e.g. "Tech Lead", "Staff Engineer"
- `[REVIEW_TRIGGER]` — e.g. "architecture changes", "breaking changes"
- `[CI_TOOL]` — GitHub Actions / CircleCI / Jenkins
- `[TEST_SUITE]` — e.g. "Jest + Cypress", "pytest"
- `[COVERAGE_THRESHOLD]` — e.g. 80
- `[LINT_TOOL]` — ESLint / Ruff / golangci-lint
- `[SECURITY_SCANNER]` — Snyk / CodeQL / Semgrep
- `[BASE_BRANCH]` — main / master / develop
- `[CD_TOOL]` — ArgoCD / Spinnaker / GitHub Actions
- `[STAGING_ENV]` / `[PROD_ENV]` — your environment names
- `[STAGING_DEPLOY_STRATEGY]` — e.g. "auto-deploy on merge"
- `[PROD_DEPLOY_STRATEGY]` — e.g. "blue-green", "canary 10→50→100%"
- `[QA_CHECKLIST]` — what QA checks
- `[APPROVAL_ROLE]` — who approves prod deploys
- `[MONITOR_DURATION]` — e.g. "30 min"
- `[MONITOR_METRICS]` — e.g. "error rate, p99 latency, CPU"
- `[RELEASE_PROCESS]` — e.g. "update CHANGELOG, tag release"
