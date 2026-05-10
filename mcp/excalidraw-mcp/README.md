# ✏️ excalidraw-mcp

> **Generate and export Excalidraw diagrams directly from your AI conversations.**

---

## What It Does

`excalidraw-mcp` is a community MCP server that lets AI assistants create, modify, and export Excalidraw diagrams programmatically. Excalidraw's hand-drawn aesthetic makes it perfect for informal architecture sketches, whiteboard-style brainstorming, and collaborative diagrams.

### Tools Exposed

| Tool | Description |
|---|---|
| `create_excalidraw` | Generate an Excalidraw diagram from a text description or structured data |
| `export_excalidraw` | Export an `.excalidraw` file to PNG or SVG |
| `update_excalidraw` | Modify an existing Excalidraw diagram |

### Why Excalidraw?

- 🖊️ **Hand-drawn style** — informal, approachable look perfect for early-stage designs
- 🤝 **Collaborative** — share live at excalidraw.com or embed in Notion/Confluence
- 📦 **JSON format** — `.excalidraw` files are plain JSON, easy to version control
- 🔌 **VS Code plugin** — edit `.excalidraw` files directly in your editor

---

## Prerequisites

- **Node.js 18+** — [nodejs.org](https://nodejs.org)
- **npx** — included with Node.js

---

## Installation

### Via npx (no install needed)

```bash
# Test
npx -y excalidraw-mcp --help
```

### Global install

```bash
npm install -g excalidraw-mcp
```

---

## Configuration

### Hermes Agent (`~/.hermes/config.yaml`)

```yaml
mcp:
  servers:
    excalidraw:
      command: npx
      args: ["-y", "excalidraw-mcp"]
      description: Generate and export Excalidraw diagrams
```

### Claude Desktop (`claude_desktop_config.json`)

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "excalidraw": {
      "command": "npx",
      "args": ["-y", "excalidraw-mcp"]
    }
  }
}
```

### Cursor IDE (`.cursor/mcp.json`)

```json
{
  "servers": {
    "excalidraw": {
      "command": "npx",
      "args": ["-y", "excalidraw-mcp"],
      "description": "Generate Excalidraw diagrams"
    }
  }
}
```

---

## Usage Examples

### Create a New Diagram

> *"Create an Excalidraw diagram showing a three-tier web architecture: client, load balancer, app servers, and database"*

The agent will:
1. Call `create_excalidraw` with a structured description
2. Save the `.excalidraw` file
3. Show you the exported PNG

### Export an Existing File

> *"Export my diagram at ./architecture.excalidraw to a PNG"*

```
Agent calls: export_excalidraw(file="./architecture.excalidraw", format="png")
Returns: /tmp/architecture.png
```

### Edit a Diagram

> *"Add a Redis cache between the load balancer and app servers in my existing diagram"*

```
Agent calls: update_excalidraw(file="./architecture.excalidraw", instruction="Add Redis cache node between load balancer and app servers with arrows")
Returns: updated .excalidraw file
```

### Example Conversation

```
You:    Sketch an Excalidraw diagram of a chat app architecture.
        Keep it informal — this is for a team whiteboard session.

Agent:  [calls create_excalidraw with component list]
        [exports to PNG]

        Here's your chat app architecture sketch:
        [shows PNG image]

        Components shown:
        • Mobile/Web clients connecting via WebSocket
        • Load balancer distributing connections
        • Chat servers (horizontally scaled)
        • Redis pub/sub for message fanout between servers
        • MongoDB for message persistence

        I've saved the .excalidraw source file so you can open and edit it
        at excalidraw.com or in VS Code with the Excalidraw extension.

        Want me to add user authentication flow or notification services?
```

---

## Opening and Editing the Output

### In Browser
1. Go to [excalidraw.com](https://excalidraw.com)
2. Click the folder icon → Open → select your `.excalidraw` file
3. Edit freely, then export as needed

### In VS Code
1. Install the [Excalidraw extension](https://marketplace.visualstudio.com/items?itemName=pomdtr.excalidraw-editor)
2. Open any `.excalidraw` file — it renders directly in the editor

### In Notion / Confluence
- Export to PNG/SVG and embed as an image
- Or use the Excalidraw embed URL if using excalidraw.com's sharing feature

---

## Troubleshooting

### "excalidraw-mcp not found"

```bash
# Verify package exists
npm search excalidraw-mcp

# Try alternative package names
npx -y @excalidraw/mcp --help
```

> ⚠️ Note: `excalidraw-mcp` is a community package. If the package name has changed, check [npmjs.com](https://www.npmjs.com/search?q=excalidraw+mcp) for the current name.

### Output file is empty or corrupt

- Check that Node.js 18+ is installed
- Try running with `--debug` flag if available
- Check temp directory write permissions: `ls -la /tmp/`

### Diagram elements are misaligned

Excalidraw uses a canvas coordinate system. For complex diagrams, it may be easier to:
1. Get the basic structure from the MCP tool
2. Open in excalidraw.com for manual refinement
3. Export the final version

---

## Resources

- [Excalidraw Official Site](https://excalidraw.com)
- [Excalidraw GitHub](https://github.com/excalidraw/excalidraw)
- [Excalidraw VS Code Extension](https://marketplace.visualstudio.com/items?itemName=pomdtr.excalidraw-editor)
- [MCP Protocol docs](https://modelcontextprotocol.io)
