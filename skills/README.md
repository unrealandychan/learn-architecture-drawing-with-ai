# 🧠 Hermes Agent Skills

This folder contains installable **Hermes Agent skills** that supercharge your AI assistant with specialized knowledge for architecture diagramming workflows.

---

## What Are Hermes Agent Skills?

Hermes Agent skills are Markdown-based knowledge files (called `SKILL.md`) that teach the AI agent how to handle specific tasks with expertise. When a skill is installed, Hermes automatically loads it when your request matches the skill's trigger conditions.

Think of skills as **expert plug-ins for your AI** — they encode best practices, decision trees, prompt templates, and common pitfall fixes into the agent's context.

---

## How to Install a Skill

### Step 1 — Locate the skill file

Each skill lives in a folder named after the skill:

```
skills/
└── architecture-diagram-with-ai/
    └── SKILL.md
```

### Step 2 — Copy to your Hermes skills directory

```bash
# Create the category directory (if it doesn't exist)
mkdir -p ~/.hermes/skills/creative/architecture-diagram-with-ai

# Copy the skill file
cp skills/architecture-diagram-with-ai/SKILL.md \
   ~/.hermes/skills/creative/architecture-diagram-with-ai/SKILL.md
```

### Step 3 — Verify the skill is registered

```bash
hermes skills list
```

You should see output like:

```
Installed Skills:
  creative/
    architecture-diagram-with-ai   Generate architecture diagrams, flowcharts, and system designs
```

### One-liner install (from repo root)

```bash
mkdir -p ~/.hermes/skills/creative/architecture-diagram-with-ai && \
cp skills/architecture-diagram-with-ai/SKILL.md ~/.hermes/skills/creative/architecture-diagram-with-ai/SKILL.md && \
echo "✅ Skill installed!"
```

---

## How Skills Are Triggered

Skills are auto-loaded when you mention relevant keywords. For the architecture diagramming skill, triggers include:

- "Draw me a system architecture for..."
- "Create a flowchart of..."
- "Generate a sequence diagram..."
- "Show me how X connects to Y"
- Any mention of: *Mermaid*, *Excalidraw*, *draw.io*, *sequence diagram*, *ER diagram*, *system design*

---

## Included Skills

| Skill | Category | Description |
|---|---|---|
| `architecture-diagram-with-ai` | `creative` | Generate architecture diagrams, flowcharts, and system designs using Mermaid, Excalidraw, or draw.io via AI prompts |

### `architecture-diagram-with-ai`

**Full path:** `~/.hermes/skills/creative/architecture-diagram-with-ai/SKILL.md`

**What it does:**
- Provides a 5-step SOP for every diagram request (Clarify → Select Tool → Generate → Present → Iterate)
- Contains a decision tree for selecting the right diagramming tool (Mermaid, Excalidraw, draw.io, PlantUML)
- Includes Mermaid syntax rules, prompting templates, and 3 complete diagram examples
- Documents MCP server integrations (`mermaid-mcp-server`, `excalidraw-mcp`)
- Lists 8+ common Mermaid parse errors with fixes
- Provides a verification checklist before delivering any diagram

**Related skills:** `mermaid-in-wiki-pages`, `architecture-diagram`

---

## Troubleshooting

### `hermes skills list` shows no skills

```bash
# Check if the skills directory exists
ls ~/.hermes/skills/

# If it doesn't exist, create it
mkdir -p ~/.hermes/skills/creative/architecture-diagram-with-ai
```

### Skill not triggering automatically

Make sure your request uses keywords the skill recognizes. Try explicitly asking:
> "Use your architecture diagram skill to create a Mermaid flowchart for..."

### Skill file not found after copy

```bash
# Verify the file exists
ls -la ~/.hermes/skills/creative/architecture-diagram-with-ai/SKILL.md

# Check file contents aren't empty
wc -l ~/.hermes/skills/creative/architecture-diagram-with-ai/SKILL.md
```

### Hermes version compatibility

Skills require **Hermes Agent v0.5+**. Check your version:

```bash
hermes --version
```

---

## Adding Your Own Skills

Skills are just Markdown files! Create your own:

```bash
mkdir -p ~/.hermes/skills/<category>/<skill-name>
cat > ~/.hermes/skills/<category>/<skill-name>/SKILL.md << 'EOF'
---
name: my-custom-skill
description: What this skill does
tags: [tag1, tag2]
---

# My Custom Skill

## 1. TRIGGER CONDITIONS
Load when the user asks about...

## 2. INSTRUCTIONS
Step-by-step guidance for the agent...
EOF
```

---

*For more information, see the [Hermes Agent documentation](https://hermes-agent.nousresearch.com/docs).*
