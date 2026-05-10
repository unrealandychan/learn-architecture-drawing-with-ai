# 📐 Draw.io (diagrams.net) — Professional Diagramming

Draw.io (also known as diagrams.net) is the most feature-rich **free** diagramming tool available. It's the go-to for professional architecture diagrams, UML, flowcharts, and network diagrams.

---

## What is Draw.io?

Draw.io is a free, open-source diagramming application available as:
- **Web app**: https://app.diagrams.net (no account needed)
- **Desktop app**: macOS, Windows, Linux
- **VS Code extension**: Edit diagrams inside VS Code
- **Confluence/Jira plugin**: Built-in for Atlassian products

**Why use Draw.io?**
- 🆓 Completely free (even the self-hosted version)
- 🏢 Professional-quality output
- 🗂️ Huge shape library (AWS, Azure, GCP, Cisco, UML...)
- 💾 Save to Google Drive, OneDrive, GitHub, or local disk
- 🔌 VS Code integration keeps diagrams next to code

---

## Quick Start

### Web App
1. Go to **https://app.diagrams.net**
2. Choose where to save: Google Drive / OneDrive / GitHub / Device
3. Start with a template or blank canvas
4. Export as PNG/SVG/PDF when done

### Desktop App
```bash
# macOS
brew install --cask drawio

# Or download from:
# https://github.com/jgraph/drawio-desktop/releases
```

### VS Code Extension
1. Open VS Code Extensions (`Ctrl+Shift+X`)
2. Search for **"Draw.io Integration"** by Henning Dieterichs
3. Install it
4. Create a file with `.drawio` or `.drawio.png` extension
5. Opens in Draw.io editor inside VS Code

---

## Key Features

### Shape Libraries
Access hundreds of professional icon sets:

| Library | Use for |
|---|---|
| **General** | Flowcharts, basic shapes |
| **AWS17** | Amazon Web Services architecture |
| **Azure** | Microsoft Azure services |
| **GCP** | Google Cloud Platform |
| **Kubernetes** | K8s cluster diagrams |
| **Network** | Cisco, network topology |
| **UML** | Class, sequence, use case |
| **Entity Relation** | Database ERDs |
| **Mockup** | UI wireframes |
| **Floorplan** | Office/building layouts |

**Enable a library:** Menu → Extras → Edit Diagram → or use left panel search

### Templates
Menu → File → New → Template:
- Software development templates
- Network diagrams
- AWS reference architectures
- Business process (BPMN)
- Mind maps

### Auto-layout
Select elements → Edit → Layout:
- **Tree**: hierarchy layouts
- **Organic**: force-directed
- **Circle**: radial
- **Grid**: align to grid

---

## Mermaid Import / Export

### Import Mermaid → Draw.io
1. Open Draw.io
2. **Extras → Edit Diagram** (or `Ctrl+Shift+X`)
3. Click the dropdown → select **"Mermaid"**
4. Paste your Mermaid code
5. Click OK — Draw.io renders it as editable shapes!

**Example — paste this in:**
```
flowchart LR
    Client --> Gateway
    Gateway --> ServiceA
    Gateway --> ServiceB
    ServiceA --> Database
    ServiceB --> Database
```

### Export Draw.io → Mermaid
Unfortunately, Draw.io doesn't natively export to Mermaid. Workflow:
1. Export as PNG/SVG for documentation
2. Keep the `.drawio` source file in your repo
3. Use the VS Code extension for `.drawio.png` format (stores diagram in PNG metadata)

### Export Formats
| Format | Best for |
|---|---|
| **PNG** | README images, Slack, presentations |
| **SVG** | Web embedding, scalable |
| **PDF** | Printing, formal docs |
| **.drawio** | Source file, editable |
| **.drawio.png** | VS Code — one file, editable + viewable |
| **XML** | Raw draw.io format |

---

## VS Code Extension Deep Dive

The **Draw.io Integration** extension by Henning Dieterichs is excellent.

### File Types
```
diagram.drawio          # Pure draw.io XML
diagram.drawio.png      # PNG with embedded draw.io XML
diagram.drawio.svg      # SVG with embedded draw.io XML
```

The `.drawio.png` format is magic: it looks like a regular PNG in GitHub/Markdown, but when opened in VS Code it's fully editable!

### Workflow with Git
```
docs/
├── system-architecture.drawio.png   ← Commit this!
└── README.md
    # Reference: ![Architecture](docs/system-architecture.drawio.png)
```

When a colleague opens `system-architecture.drawio.png` in VS Code with the extension installed, they can edit it directly. When pushed to GitHub, it displays as a regular image.

### Setting Custom Color Themes
The VS Code extension supports themes. Add to your VS Code `settings.json`:
```json
{
  "hediet.vscode-drawio.theme": "dark"
}
```

---

## Tips for Professional Diagrams

### 1. Use Official Cloud Icons
Don't use generic boxes for AWS/Azure services. Use official icons:
- **View → Shapes → AWS17** (or Azure/GCP)
- Drag official service icons onto canvas
- Your diagrams look like official architecture documents

### 2. Connection Points & Waypoints
- Right-click a shape → **Edit Connection Points** to control where arrows connect
- Right-click an arrow → **Add Waypoint** to route arrows around shapes

### 3. Containers & Groups
- Drag one shape onto another to make it a **container** (parent-child grouping)
- Use for: VPCs, availability zones, microservice boundaries
- Right-click → **Select Descendants** to select everything inside

### 4. Swim Lanes
Insert → Layout → Horizontal/Vertical Swim Lanes:
- Perfect for: business processes, team responsibilities, event flows

### 5. Page Tabs
Add multiple pages (like Excel sheets) at the bottom:
- Page 1: High-level overview
- Page 2: Backend detail
- Page 3: Database schema
- Page 4: Network topology

### 6. Link to Other Pages
Right-click a shape → **Edit Link** → select "Page" → choose a page tab
Creates clickable navigation in diagrams — great for presentations.

### 7. Styles & Themes
Apply consistent styling:
- Select shapes → right-click → **Edit Style**
- Use **Format Panel** (right side) for color, font, border
- **Edit → Style** → copy style string to reuse across shapes

---

## Collaboration

### Google Drive
1. Save to Google Drive
2. Share the Google Drive file with collaborators
3. ⚠️ Only one person edits at a time (no real-time co-editing)

### draw.io Confluence Plugin
- Real-time collaboration built in
- Versioned with Confluence pages
- Best for enterprise teams using Atlassian stack

### GitHub Workflow
```bash
# Save diagram as .drawio.png in your repo
git add docs/architecture.drawio.png
git commit -m "docs: update system architecture diagram"
git push
```
GitHub renders the PNG in markdown, VS Code users can edit it.

---

## Draw.io vs Other Tools

| Feature | Draw.io | Excalidraw | Mermaid |
|---|---|---|---|
| Learning curve | Medium | Low | Low-Medium |
| Output quality | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Cloud icon libraries | ✅ Extensive | ⚠️ Limited | ❌ None |
| Version control | ⚠️ Binary (use .drawio.png) | ⚠️ Binary | ✅ Text |
| Collaboration | ⚠️ Not real-time | ✅ Real-time | ❌ |
| Mermaid import | ✅ Yes | ✅ Yes | N/A |
| Free | ✅ | ✅ | ✅ |
| Offline | ✅ Desktop app | ❌ | ✅ |

---

## Resources

- 📖 [Draw.io Documentation](https://www.drawio.com/doc/)
- 🔌 [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=hediet.vscode-drawio)
- 🎥 [Draw.io YouTube Channel](https://www.youtube.com/c/drawio)
- 🏗️ [AWS Architecture Icons for Draw.io](https://aws.amazon.com/architecture/icons/)
- ⭐ [GitHub (diagrams.net)](https://github.com/jgraph/drawio)

---

> ➡️ Next: See [04-ai-prompts/README.md](../04-ai-prompts/README.md) — the main guide for using AI to generate all your diagrams!
