# 🔌 MCP Server Integrations

This folder contains **Model Context Protocol (MCP)** server configurations for AI-assisted architecture diagramming.

---

## What is MCP (Model Context Protocol)?

**Model Context Protocol (MCP)** is an open standard developed by Anthropic that lets AI assistants communicate with external tools and data sources through a unified interface. Think of it as a "plugin system" for AI — instead of training the model to know everything, you give it live access to specialized tools.

With MCP, your AI assistant can:
- **Call tools** — execute code, render images, query databases
- **Access resources** — read files, fetch URLs, connect to APIs
- **Use prompts** — load structured templates for specific tasks

MCP servers run as local processes (or remote services) and expose capabilities that the AI can invoke via standardized tool calls.

**Learn more:** [modelcontextprotocol.io](https://modelcontextprotocol.io)

---

## Why Use MCP for Diagrams?

Without MCP, an AI can only *write* Mermaid code — you then copy-paste it into a renderer yourself.

With MCP diagram servers, the AI can:
- ✅ **Render diagrams instantly** — no copy-paste, no browser tab
- ✅ **Validate syntax** before showing you output
- ✅ **Export to PNG/SVG** directly in the conversation
- ✅ **Iterate faster** — the agent can test and fix diagrams automatically

---

## Included MCP Configurations

| Folder | Server | Description |
|---|---|---|
| `mermaid-mcp-server/` | [mermaid-mcp-server](https://github.com/peng-shawn/mermaid-mcp-server) | Render and validate Mermaid diagrams via MCP tool calls |
| `excalidraw-mcp/` | excalidraw-mcp | Generate and export Excalidraw diagrams |

---

## General Installation Steps

Every MCP server follows the same basic setup pattern:

### 1. Install the server

Most Node.js MCP servers can be run without installing via `npx`:

```bash
# Test without installing
npx -y mermaid-mcp-server --help

# Or install globally
npm install -g mermaid-mcp-server
```

### 2. Configure your AI client

Add the server to your AI client's config file (see client-specific sections below).

### 3. Restart the client

MCP servers are loaded at startup. Restart your AI client after adding config.

### 4. Verify the tools are available

Ask your AI: *"What MCP tools do you have available?"* — it should list the diagram tools.

---

## How to Configure in Hermes Agent

Add to `~/.hermes/config.yaml`:

```yaml
mcp:
  servers:
    mermaid:
      command: npx
      args: ["-y", "mermaid-mcp-server"]
      description: Render and validate Mermaid diagrams
    excalidraw:
      command: npx
      args: ["-y", "excalidraw-mcp-server"]
      description: Generate Excalidraw diagrams
```

Restart Hermes after saving. Verify with:
```bash
hermes mcp list
```

---

## How to Configure in Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "mermaid": {
      "command": "npx",
      "args": ["-y", "mermaid-mcp-server"]
    },
    "excalidraw": {
      "command": "npx",
      "args": ["-y", "excalidraw-mcp-server"]
    }
  }
}
```

Restart Claude Desktop after saving. You'll see a 🔌 icon in the interface when MCP servers are active.

---

## How to Configure in Cursor / VS Code

Create or edit `.cursor/mcp.json` in your project root (Cursor) or `.vscode/mcp.json` (VS Code with MCP extension):

```json
{
  "servers": {
    "mermaid": {
      "command": "npx",
      "args": ["-y", "mermaid-mcp-server"],
      "description": "Render Mermaid diagrams"
    },
    "excalidraw": {
      "command": "npx",
      "args": ["-y", "excalidraw-mcp-server"],
      "description": "Generate Excalidraw diagrams"
    }
  }
}
```

For **global** Cursor config, edit `~/.cursor/mcp.json`.

---

## Troubleshooting

### MCP server not showing up

1. Check that Node.js 18+ is installed: `node --version`
2. Test the server manually: `npx -y mermaid-mcp-server --help`
3. Check the config file has valid JSON/YAML
4. Restart the AI client completely

### `npx` command not found

Install Node.js from [nodejs.org](https://nodejs.org) or via nvm:
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 20
nvm use 20
```

### Permission errors on macOS

```bash
# Give npx permission to run
sudo xattr -rd com.apple.quarantine $(which npx)
```

### Tools not being called by the AI

Ask the AI explicitly: *"Please use the mermaid MCP tool to render this diagram"*. Some clients require explicit tool invocation for first use.

### Slow first run

`npx -y` downloads the package on first run. This is normal — subsequent runs use the cache and are instant.

---

*Each subfolder contains a detailed README for that specific MCP server.*
