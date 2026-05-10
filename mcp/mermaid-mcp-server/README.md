# 🧜 mermaid-mcp-server

> **Render and validate Mermaid diagrams directly inside your AI conversations.**

**GitHub:** https://github.com/peng-shawn/mermaid-mcp-server

---

## What It Does

`mermaid-mcp-server` is an MCP (Model Context Protocol) server that exposes Mermaid diagram rendering as AI-callable tools. Instead of generating Mermaid code and asking you to paste it into a browser, your AI assistant can:

- 🎨 **Render diagrams to PNG or SVG** — get the actual image in the conversation
- ✅ **Validate Mermaid syntax** — catch errors before showing you anything
- 🔄 **Iterate automatically** — fix parse errors and re-render in one step
- 📁 **Save output files** — rendered images saved to a local path you control

### Tools Exposed

| Tool | Description |
|---|---|
| `render_mermaid` | Render Mermaid code to PNG or SVG image |
| `validate_mermaid` | Check Mermaid syntax for errors without rendering |

---

## Prerequisites

- **Node.js 18+** — [Download from nodejs.org](https://nodejs.org) or install via nvm
- **npx** — included with Node.js 7+

Check your versions:
```bash
node --version   # must be v18.0.0 or higher
npx --version    # must be 7.0.0 or higher
```

---

## Installation Methods

### Method 1: npx (Recommended — No Install Required)

Run directly without installing. The package is downloaded and cached automatically:

```bash
# Test it works
npx -y mermaid-mcp-server --help
```

This is the **recommended approach** for MCP configs — use `npx` as the command so the server always runs the latest version without manual updates.

### Method 2: Global Install

For faster startup (no download delay on first run):

```bash
npm install -g mermaid-mcp-server

# Verify
mermaid-mcp-server --help
```

Then use `mermaid-mcp-server` as the command in your config instead of `npx`.

---

## Configuration

### Hermes Agent (`~/.hermes/config.yaml`)

```yaml
mcp:
  servers:
    mermaid:
      command: npx
      args: ["-y", "mermaid-mcp-server"]
      description: Render and validate Mermaid diagrams
```

> 📄 Ready-to-copy file: [`config-examples/hermes-config.yaml`](./config-examples/hermes-config.yaml)

### Claude Desktop (`claude_desktop_config.json`)

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`  
**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

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

> 📄 Ready-to-copy file: [`config-examples/claude-desktop-config.json`](./config-examples/claude-desktop-config.json)

### Cursor IDE (`.cursor/mcp.json`)

```json
{
  "servers": {
    "mermaid": {
      "command": "npx",
      "args": ["-y", "mermaid-mcp-server"],
      "description": "Render and validate Mermaid diagrams"
    }
  }
}
```

> 📄 Ready-to-copy file: [`config-examples/cursor-mcp.json`](./config-examples/cursor-mcp.json)

---

## Usage Examples

### Basic Rendering

Ask your AI:
> *"Create a Mermaid flowchart for a CI/CD pipeline and render it as a PNG"*

The agent will:
1. Generate the Mermaid code
2. Call `validate_mermaid` to check syntax
3. Call `render_mermaid` to produce the image
4. Show you the rendered diagram

### Explicit Tool Invocation

You can ask the AI to use the tool directly:

> *"Use the mermaid MCP tool to render this diagram:*
> ```mermaid
> graph TD
>   A[Developer] --> B[Git Push]
>   B --> C[CI Pipeline]
>   C --> D[Tests Pass?]
>   D -- Yes --> E[Deploy to Staging]
>   D -- No --> F[Notify Developer]
> ```"

### Validate Before Render

> *"Validate this Mermaid code and fix any errors, then render it:"*

The agent will call `validate_mermaid` first, fix any issues, then render.

---

## Example Conversation Flow

```
You:    Draw me a microservices architecture for an e-commerce app.
        Include: API Gateway, User Service, Product Service, Order Service,
        a message queue, and databases for each service.

Agent:  I'll create that architecture diagram for you.
        [generates Mermaid code]
        [calls validate_mermaid → valid ✓]
        [calls render_mermaid → /tmp/ecommerce-arch.png]

        Here's your microservices architecture:
        [shows rendered PNG image]

        The diagram shows:
        • API Gateway as the single entry point
        • 3 domain services each with their own database (Database-per-Service pattern)
        • RabbitMQ message queue for async communication between Order and Notification services

        Would you like me to:
        • Add a CDN layer and load balancer?
        • Show the authentication flow as a sequence diagram?
        • Export this to draw.io format?

You:    Yes, add a Redis cache layer between the API Gateway and services.

Agent:  [makes targeted edit to existing diagram]
        [validates → valid ✓]
        [re-renders → /tmp/ecommerce-arch-v2.png]

        Updated! I added a Redis cache cluster between the Gateway and the
        three services. Cache hits return without reaching downstream services.
```

---

## Troubleshooting

### "command not found: npx"

Node.js is not installed or not in PATH:
```bash
# Install Node.js via nvm (recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc  # or ~/.zshrc
nvm install 20
nvm use 20

# Verify
node --version && npx --version
```

### Slow first startup (30+ seconds)

Normal on first run — `npx` is downloading the package. Subsequent runs use cache and start in ~1 second. To pre-cache:
```bash
npx -y mermaid-mcp-server --version
```

### "Render failed: browser not found"

mermaid-mcp-server uses Puppeteer/Chromium for rendering. If Chromium is missing:
```bash
# Install Chromium
npx puppeteer browsers install chrome

# Or on Ubuntu/Debian
sudo apt-get install -y chromium-browser
```

### MCP server connects but tools aren't called

Ask explicitly: *"Please use the render_mermaid MCP tool to render this"*. 

Also check that the tool names in your prompts match: `render_mermaid` and `validate_mermaid`.

### Output path permission errors

The server writes rendered files to a temp directory. If you see permission errors:
```bash
# Check temp directory permissions
ls -la /tmp/

# Or specify a custom output directory in the server config:
# (check server docs for --output-dir flag)
```

### Diagram renders but looks wrong

Common issues:
- **Labels with special chars** — wrap in quotes: `A["Label (with parens)"]`
- **Duplicate node IDs** — use unique IDs for every node
- **Wrong arrow syntax** — use `-->` not `->` in flowcharts

Use `validate_mermaid` first to catch syntax issues before rendering.

---

## Resources

- [mermaid-mcp-server on GitHub](https://github.com/peng-shawn/mermaid-mcp-server)
- [Mermaid.js documentation](https://mermaid.js.org/intro/)
- [Mermaid Live Editor](https://mermaid.live) — test diagrams in browser
- [MCP Protocol docs](https://modelcontextprotocol.io)
- [MCP server list](https://github.com/modelcontextprotocol/servers)
