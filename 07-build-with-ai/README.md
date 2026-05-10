# ⚡ Build with AI — The Complete Speed Workflow

> From idea to shareable diagram in 5 minutes, not 2 hours.

## The Problem

Traditional diagramming:
- Open tool → drag boxes → connect arrows → resize → align → format text → 2 hours later → still looks bad 😭

AI-first diagramming:
- Describe idea → AI generates → review → export → done ✅

## The Speed Formula

**Step** | **Action** | **Time**
--- | --- | ---
1 | Use AI Clarifier Prompt | 30 sec
2 | Generate Mermaid with AI | 30 sec
3 | Review in mermaid.live | 1 min
4 | Refine with AI if needed | 1 min
5 | Convert to target platform | 1 min
6 | Export and share | 30 sec
**Total** | | **~5 min**

## The Decision Flowchart

```mermaid
flowchart TD
    A[💡 Got an idea] --> B{Know what\nto draw?}
    B -->|No| C[🤖 Use Clarifier Prompt]
    B -->|Yes| D[Choose diagram type]
    C --> D
    D --> E{Who is the audience?}
    E -->|Developers| F[Mermaid in docs / GitHub]
    E -->|Stakeholders| G[Draw.io or Lucidchart]
    E -->|Design team| H[Figma or Excalidraw]
    E -->|Self / Notes| I[Excalidraw or Notion]
    F & G & H & I --> J[🤖 Generate with AI]
    J --> K[⌨️ Review + refine with hotkeys]
    K --> L{Happy?}
    L -->|No| M[🤖 Refinement Prompt]
    M --> K
    L -->|Yes| N[🔄 Convert + Export]
    N --> O[✅ Share!]
```

## Chapters in This Section

**File** | **What you'll learn**
--- | ---
[01 - AI Prompt Chain](./01-ai-prompt-chain.md) | The 3-prompt strategy: clarify → generate → refine
[02 - Platform Conversion](./02-platform-conversion.md) | Mermaid → Draw.io, Excalidraw, Figma, Notion, GitHub
[03 - Hotkeys Cheatsheet](./03-hotkeys-cheatsheet.md) | Speed shortcuts for every tool
[04 - Real Session Demo](./04-real-session-demo.md) | Watch a full 5-min session, copy the prompts

## The Conversion Map

```mermaid
flowchart LR
    M[🧜 Mermaid Code]
    M --> ML[mermaid.live\n→ PNG / SVG]
    M --> DIO[Draw.io import\n→ PDF / PNG / SVG / XML]
    M --> EX[Excalidraw import\n→ PNG / SVG / .excalidraw]
    M --> GH[GitHub README\n→ Rendered natively]
    M --> NO[Notion /code block\n→ Rendered natively]
    M --> FIG[Figma plugin\n→ Design components]
    M --> CF[Confluence plugin\n→ Wiki page]
    M --> VS[VS Code preview\n→ Live preview]
```

---

> ⭐ **New to this repo?** Start here — this chapter ties everything together.
