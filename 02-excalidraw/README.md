# ✏️ Excalidraw — Whiteboard-Style Architecture Diagrams

Excalidraw is a **virtual whiteboard** for sketching hand-drawn style diagrams. Perfect for brainstorming, rough architecture sketches, and collaborative design sessions.

---

## What is Excalidraw?

Excalidraw is an open-source, browser-based diagramming tool that produces diagrams with a **hand-drawn aesthetic**. It's the go-to tool when you want:

- 🧠 **Brainstorming** — quick rough sketches
- 🤝 **Collaboration** — real-time multi-user editing
- 🎨 **Visual appeal** — the sketchy look signals "work in progress" (great for proposals)
- 📋 **Whiteboard sessions** — online equivalent of a physical whiteboard

**Live editor:** https://excalidraw.com (no signup needed)

---

## When to Use Excalidraw vs Mermaid

| Scenario | Excalidraw | Mermaid |
|---|---|---|
| Quick brainstorm | ✅ Perfect | ❌ Too rigid |
| Documentation in repo | ❌ Binary file | ✅ Text/diff-able |
| Collaborative session | ✅ Real-time | ❌ Not collaborative |
| Presentation slides | ✅ Looks great | ⚠️ Functional only |
| Auto-layout | ❌ Manual | ✅ Auto |
| Version control | ⚠️ JSON file | ✅ Plain text |

---

## Getting Started

### Option 1: Web App (Fastest)
1. Go to **https://excalidraw.com**
2. Start drawing immediately — no account needed
3. Share via link for real-time collaboration

### Option 2: VS Code Extension
1. Install **"Excalidraw" by Poview** from the VS Code marketplace
2. Create a file with `.excalidraw` extension
3. Open it — renders directly in VS Code

### Option 3: Self-hosted / Obsidian
- **Obsidian**: Install the Excalidraw plugin
- **Notion**: Embed via Excalidraw share link
- **Self-host**: Deploy with Docker from [excalidraw/excalidraw](https://github.com/excalidraw/excalidraw)

---

## Core Tools & Features

### Drawing Tools
| Tool | Shortcut | Use for |
|---|---|---|
| Selection | `V` or `1` | Select & move elements |
| Rectangle | `R` or `3` | Servers, services, databases |
| Diamond | `4` | Decision points |
| Ellipse | `E` or `5` | Users, external systems |
| Arrow | `A` or `7` | Data flow, connections |
| Line | `L` or `6` | Simple connections |
| Text | `T` or `8` | Labels, annotations |
| Freedraw | `P` or `9` | Freehand sketches |
| Eraser | `0` | Delete elements |

### Keyboard Shortcuts
```
Ctrl+Z         Undo
Ctrl+Shift+Z   Redo
Ctrl+A         Select all
Ctrl+G         Group selection
Ctrl+Shift+G   Ungroup
Ctrl+D         Duplicate
Ctrl+L         Lock/unlock element
Ctrl+F         Find element
Space + drag   Pan canvas
Ctrl+scroll    Zoom in/out
```

---

## AI Integration: Text-to-Diagram

Excalidraw has **built-in AI diagramming** (DiagramToCode and text-to-diagram):

### Method 1: Mermaid → Excalidraw
1. Click the **wand icon** (top toolbar) → "Generate diagram"
2. Or: Menu → Extra tools → Mermaid to Excalidraw
3. Paste your Mermaid code → converts to editable Excalidraw shapes

### Method 2: Text Description → Diagram
Use AI tools to generate Mermaid first, then import:

**Prompt to use with ChatGPT/Claude:**
```
I want to create an Excalidraw diagram of [your system].

First, generate a Mermaid flowchart LR for the architecture.
Include:
- [Component 1]
- [Component 2]
- [Component 3]

Then I'll import the Mermaid into Excalidraw using the Mermaid import feature.
```

### Method 3: Excalidraw AI Plugin (Paid)
The Excalidraw+ subscription includes AI that can:
- Convert text descriptions to diagrams
- Recognize and organize hand-drawn shapes
- Suggest diagram improvements

---

## Tips for Architecture Diagrams

### 1. Use Color Coding Consistently
Establish a color system:
- 🔵 **Blue** = Client-facing / Frontend
- 🟢 **Green** = Backend services
- 🟡 **Yellow** = Data stores / Databases
- 🔴 **Red** = External services / 3rd party
- ⚫ **Grey** = Infrastructure / DevOps

### 2. Use Frames for Grouping
Press `F` to create a frame — perfect for grouping a "layer" of your architecture:
- Frame 1: "Client Layer"
- Frame 2: "API Layer"
- Frame 3: "Data Layer"

### 3. Arrows with Labels
Double-click any arrow to add a label. Use it to show:
- Protocol: `REST`, `gRPC`, `WebSocket`
- Data: `user events`, `order data`
- Direction: `→ read`, `← write`

### 4. Use Libraries for Icons
Click the library icon (bottom left) → search for:
- **AWS Architecture** icons
- **Azure** icons
- **GCP** icons
- **Database** icons (cylinder shapes)

### 5. The Rough Style = "Work in Progress"
The sketchy aesthetic is a **feature**, not a bug. It signals to viewers:
> "This is a proposal/draft — feedback welcome!"

Switch to "Sharp" style for final/polished diagrams.

---

## Export Options

### PNG Export
- File → Export image → PNG
- Options: background, scale (2x, 3x for retina)
- Best for: presentations, Slack, emails

### SVG Export
- File → Export image → SVG
- Scalable, editable in Figma/Illustrator
- Best for: websites, documentation

### JSON Export (.excalidraw)
- File → Save to disk
- JSON format — can be version-controlled
- Best for: storing in Git repo

### Excalidraw+ (Cloud)
- Auto-save to cloud
- Share with link
- Version history

### Embed in Notion
```
1. Export as PNG
2. Upload to Notion page
OR
1. Share Excalidraw link
2. Embed in Notion as iframe
```

---

## Collaboration Features

### Real-time Collaboration
1. Click **"Share"** (top right)
2. Copy the collaboration link
3. Share with teammates → everyone edits simultaneously
4. No account required!

### Cursor tracking
- See other users' cursors in real-time
- Each user gets a unique color

### Presentation Mode
- Press `Alt+P` to enter presentation mode
- Full-screen, no toolbars — clean for demos

---

## Saving Diagrams in Your Repo

To store Excalidraw diagrams alongside code:

```
my-project/
├── docs/
│   ├── architecture.excalidraw     # Editable source
│   ├── architecture.png            # Exported image for README
│   └── architecture.svg            # Scalable version
└── README.md
    # Reference: ![Architecture](docs/architecture.png)
```

**Automate PNG export** with Excalidraw CLI:
```bash
npx @excalidraw/export-cli export \
  --input docs/architecture.excalidraw \
  --output docs/architecture.png \
  --scale 2
```

---

## Resources

- 📖 [Excalidraw Docs](https://docs.excalidraw.com)
- 🎥 [Official YouTube tutorials](https://www.youtube.com/@excalidraw)
- 🎨 [Excalidraw Libraries](https://libraries.excalidraw.com)
- 💬 [Discord Community](https://discord.gg/UexuTaE)
- ⭐ [GitHub](https://github.com/excalidraw/excalidraw)

---

> ➡️ Next: Check out [03-drawio/README.md](../03-drawio/README.md) for a more feature-rich diagramming tool.
