# 🏗️ Learn Architecture Drawing with AI

> **Stop drawing boxes manually. Let AI do the heavy lifting.**

![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)
![GitHub Stars](https://img.shields.io/github/stars/learn-architecture-drawing-with-ai?style=social)

A practical guide to creating professional architecture diagrams, flowcharts, and system designs — using AI tools to go from idea to diagram in seconds.

---

## 🎯 Who Is This For?

- **Developers** who hate spending hours in drag-and-drop tools
- **Tech leads** who need to document systems quickly
- **Students** learning system design
- **Anyone** who wants beautiful diagrams without the pain

---

## 🗺️ Table of Contents

- [📚 Tutorial Chapters](#-what-youll-learn)
- [⚡ Quick Start](#-quick-start--your-first-ai-diagram-in-60-seconds)
- [🛠️ Tool Comparison](#️-tool-comparison)
- [🤖 The AI-First Workflow](#-the-ai-first-workflow)
- [🧠 Hermes Agent Skills](#-hermes-agent-skills)
- [🔌 MCP Server Integrations](#-mcp-server-integrations)
- [🤝 Contributing](#-contributing)
- [📝 License](#-license)

---

## 📚 What You'll Learn

**Chapter** | **Tool** | **What You'll Make**
--- | --- | ---
[01 - Mermaid](./01-mermaid/) | Mermaid.js | Flowcharts, sequence diagrams, ER diagrams
[02 - Excalidraw](./02-excalidraw/) | Excalidraw | Hand-drawn style architecture diagrams
[03 - Draw.io](./03-drawio/) | Draw.io / diagrams.net | Professional enterprise diagrams
[04 - AI Prompts](./04-ai-prompts/) | ChatGPT / Claude / Gemini | Generate any diagram from plain English
[05 - Real World](./05-real-world/) | All tools | Netflix, Uber, WhatsApp architectures
[07 - ⚡ Build with AI](./07-build-with-ai/) | All tools | **The complete AI+Human speed workflow** ⭐ *Start here*
[skills/](./skills/) | Hermes Agent | Installable AI skill for diagram generation
[mcp/](./mcp/) | MCP Servers | Live diagram rendering inside AI conversations

---

## ⚡ Quick Start — Your First AI Diagram in 60 Seconds

1. Go to [mermaid.live](https://mermaid.live)
2. Paste this prompt into ChatGPT / Claude:

```
Draw a system architecture diagram in Mermaid syntax for a URL shortener service.
Include: user, load balancer, API server, Redis cache, PostgreSQL database, and CDN.
```

3. Copy the output into Mermaid Live Editor
4. 🎉 Done — you have a professional diagram

---

## 🛠️ Tool Comparison

**Tool** | **Style** | **Learning Curve** | **AI Support** | **Best For**
--- | --- | --- | --- | ---
**Mermaid** | Code-based | ⭐⭐ Low | ✅ Excellent | Docs, GitHub, code reviews
**Excalidraw** | Hand-drawn | ⭐ Very Low | ✅ Good | Whiteboard sessions, rough designs
**Draw.io** | Professional | ⭐⭐⭐ Medium | ✅ Good | Enterprise, presentations
**PlantUML** | Code-based | ⭐⭐⭐ Medium | ✅ Good | UML purists, Java ecosystem
**Lucidchart** | Professional | ⭐⭐ Low | ✅ Good | Team collaboration (paid)

---

## 🤖 The AI-First Workflow

```
You describe → AI generates code → You paste → Done ✅
```

Instead of:
```
You open tool → drag boxes → connect arrows → resize → align → 
format text → 2 hours later → still looks bad 😭
```

---

## 🧠 Hermes Agent Skills

The [`skills/`](./skills/) folder contains an installable **Hermes Agent skill** that gives your AI assistant deep expertise in architecture diagramming — decision trees, prompt templates, syntax rules, and error fixes baked in.

### Quick Install

```bash
# Create skill directory
mkdir -p ~/.hermes/skills/creative/architecture-diagram-with-ai

# Copy skill file
cp skills/architecture-diagram-with-ai/SKILL.md \
   ~/.hermes/skills/creative/architecture-diagram-with-ai/SKILL.md

# Verify
hermes skills list
```

### What the Skill Adds

- 🗺️ **5-step SOP** for every diagram request (Clarify → Select → Generate → Present → Iterate)
- 🌳 **Tool decision tree** — automatically picks Mermaid vs Excalidraw vs draw.io
- 📝 **Prompt templates** for architecture, sequence, ER, and class diagrams
- 🐛 **8 common error fixes** for Mermaid parse errors
- ✅ **Verification checklist** before delivering any diagram
- 🔌 **MCP-aware** — uses mermaid-mcp-server when available for live rendering

📖 [Full skill documentation →](./skills/README.md)

---

## 🔌 MCP Server Integrations

The [`mcp/`](./mcp/) folder contains **Model Context Protocol (MCP)** server configurations that let your AI assistant render diagrams live — no copy-paste required.

### What is MCP?

MCP is an open standard that lets AI assistants call external tools. With diagram MCP servers, your AI can render Mermaid to PNG, validate syntax, and deliver images directly in the conversation.

### Quick Install — Mermaid MCP

**Hermes Agent** (`~/.hermes/config.yaml`):
```yaml
mcp:
  servers:
    mermaid:
      command: npx
      args: [-y, mermaid-mcp-server]
      description: Render and validate Mermaid diagrams
```

**Claude Desktop** (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "mermaid": {
      "command": "npx",
      "args": ["-y", "mermaid-mcp-server"]
    }
  }
}
```

**Cursor** (`.cursor/mcp.json`):
```json
{
  "servers": {
    "mermaid": {
      "command": "npx",
      "args": ["-y", "mermaid-mcp-server"]
    }
  }
}
```

### Included MCP Servers

- 🧜 **[mermaid-mcp-server](./mcp/mermaid-mcp-server/)** — Render and validate Mermaid diagrams
- ✏️ **[excalidraw-mcp](./mcp/excalidraw-mcp/)** — Generate Excalidraw whiteboard diagrams

📖 [Full MCP documentation →](./mcp/README.md)

---

## 🤝 Contributing

Contributions are welcome! Here's how to help:

### Add a Tutorial Chapter

1. Fork the repo
2. Create a new folder: `06-your-topic/`
3. Add a `README.md` with your tutorial
4. Include at least 2 example diagrams
5. Open a pull request

### Add an AI Prompt

Add your best diagram prompts to [`04-ai-prompts/`](./04-ai-prompts/). Each prompt should:
- Be copy-paste ready
- Specify the diagram type
- Include expected output description

### Add a Real-World Case Study

Add to [`05-real-world/`](./05-real-world/) — any well-known system architecture works (e-commerce, social media, streaming, etc.)

### Add an MCP Server Config

Add a new subfolder under [`mcp/`](./mcp/) with:
- `README.md` — setup guide
- `config-examples/` — ready-to-copy config files for Hermes, Claude, and Cursor

### Guidelines

- Keep examples practical and runnable
- Test all Mermaid code in [mermaid.live](https://mermaid.live) before submitting
- Follow the existing folder structure
- One clear concept per file

### Reporting Issues

Found a broken diagram or incorrect syntax? [Open an issue](../../issues) with:
- The broken code snippet
- The error message
- Your rendering environment (GitHub, mermaid.live, VS Code, etc.)

---

## 🌟 Star This Repo

If this helped you, please ⭐ star the repo — it helps others find it!

---

## 📝 License

MIT — use freely, share widely.
