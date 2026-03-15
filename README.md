# Figma Console MCP Server

[![MCP](https://img.shields.io/badge/MCP-Compatible-blue)](https://modelcontextprotocol.io/)
[![npm](https://img.shields.io/npm/v/figma-console-mcp)](https://www.npmjs.com/package/figma-console-mcp)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Sponsor](https://img.shields.io/badge/Sponsor-southleft-ea4aaa?logo=github-sponsors&logoColor=white)](https://github.com/sponsors/southleft)

> Connect **Claude Desktop** to **Figma Desktop** and design, inspect, and manage your files through natural language.

> **Note:** This is a beginner-friendly adaptation of the [original Figma Console MCP repository](https://github.com/southleft/figma-console-mcp/tree/main?tab=readme-ov-file). I've simplified the setup guide and focused it on Claude Desktop so it's easier to get started. If you want the full documentation — including NPX setup, Cloud Mode, Remote SSE, and advanced options — visit the original repo.

---

## What is this?

Figma Console MCP is a Model Context Protocol server that gives Claude direct access to your Figma files. Once connected, you can ask Claude to read your design data, create UI components, manage design tokens, debug plugins, and audit your design system — all without leaving the conversation.

---

## What Claude Can Do With Your Figma File


### Create & Design
```
Create a login card with email and password fields and a Sign In button
Design a navbar with logo, links, and a user avatar on the right
Build a settings page with a sidebar and form fields in the main area
Create a modal dialog with a header, content area, and Save/Cancel buttons
```

### Manage Design Tokens
```
Create a "Brand Colors" token collection with Light and Dark modes
Add a primary color variable — #3B82F6 for Light, #60A5FA for Dark
Rename the "Default" mode to "Light Theme"
Add a "High Contrast" mode to my existing token collection
```

### Analyze & Audit
```
Audit my design system and show me a health score
Compare the Button component in Figma against our React implementation
Run an accessibility lint on this frame and flag WCAG issues
Browse the design tokens interactively
```

---

## Setup Guide — Claude Desktop + Figma Desktop

This guide connects **Claude Desktop** to **Figma Desktop** using the **Figma Claude Connect** plugin.

**Connection architecture:**

```
Claude Desktop
      ↓
Local MCP Server (Node)
      ↓ WebSocket
Figma Claude Connect Plugin
      ↓
Figma API
```

---

### Prerequisites

| Tool | Requirement |
|------|-------------|
| Node.js | Version 18+ |
| Git | Installed |
| Figma | Desktop app |
| Claude | Claude Desktop |

---

### Step 0 — Install Node.js (If Needed)

Check if Node.js is installed:

```bash
node -v
```

If you see `v18.x.x` or higher, skip to Step 1.

If not:
1. Go to [nodejs.org](https://nodejs.org)
2. Download and install **Node.js LTS**
3. Verify with `node -v`

---

### Step 1 — Clone the Repository

```bash
git clone https://github.com/southleft/figma-console-mcp.git
```
### then
```bash
cd figma-console-mcp
```
---

### Step 2 — Install Dependencies

```bash
npm install
```

---

### Step 3 — Build the Local MCP Server

```bash
npm run build:local
```

This generates `dist/local.js` — the file Claude Desktop will run.

---

### Step 4 — Generate a Figma API Token

1. Go to **Figma → Settings → Personal access tokens**
   - Direct link: [figma.com/developers/api#access-tokens](https://www.figma.com/developers/api#access-tokens)
2. Click **Generate new token**
3. Name it `Claude MCP`
4. Copy the token — it starts with `figd_` and you won't see it again

---

### Step 5 — Configure Claude Desktop

Open the Claude Desktop config folder:

**macOS:** `~/Library/Application Support/Claude/`

**Windows:** `%APPDATA%\Claude\`

Create (or edit) `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "figma-console": {
      "command": "node",
      "args": [
        "/Users/YOUR_USERNAME/figma-console-mcp/dist/local.js"
      ],
      "env": {
        "FIGMA_ACCESS_TOKEN": "YOUR_FIGMA_TOKEN"
      }
    }
  }
}
```

Replace `YOUR_USERNAME` with your actual username and `YOUR_FIGMA_TOKEN` with the token from Step 4.

> **Tip:** The path in `args` must be the full absolute path to `dist/local.js`. Run `pwd` inside the `figma-console-mcp` folder to get it.

---

### Step 6 — Restart Claude Desktop

Completely quit Claude Desktop and reopen it. Claude will start the MCP server automatically on launch.

---

### Step 7 — Install the Figma Claude Connect Plugin

Open **Figma Desktop**, then:

```
Plugins → Development → Import plugin from manifest...
```

Select `figma-claude-connect/manifest.json` from the `figma-console-mcp` directory.

The plugin will appear as **Figma Claude Connect** in your Development plugins. This is a one-time import — it persists across Figma restarts.

---

### Step 8 — Run the Plugin

Open your Figma file and run:

```
Plugins → Development → Figma Claude Connect
```

The plugin window will appear. Expected status:

```
● MCP ready
```

This confirms the plugin connected to the MCP server via WebSocket.

---

### Step 9 — Test the Connection

In **Claude Desktop**, ask:

```
What Figma tools are available?
```

Claude should respond with available MCP tools. Then try:

```
Check Figma status
```

```
List variables in my Figma file
```

```
Take a screenshot of the current Figma canvas
```

---

## Important: Keep These Open

For the integration to work, all three must be running simultaneously:

1. **Claude Desktop**
2. **Figma Desktop**
3. **Figma Claude Connect plugin window**

Closing the plugin window stops the connection.

---

## Cloud Mode (Optional)

If you prefer to use a web-based AI client (Claude.ai, v0, Replit, or Lovable) instead of Claude Desktop, Cloud Mode lets you connect without Node.js. In the plugin, toggle **Cloud Mode**, then tell your AI client "Connect to my Figma plugin" to receive a 6-character pairing code. Enter it in the plugin to establish the connection.

Cloud Mode gives you 43 tools including full design creation and token management. Real-time console monitoring is only available in Local Mode.

---

## Troubleshooting

**Plugin shows "connecting" and never reaches MCP ready**
→ Restart Claude Desktop, then re-run the plugin in Figma.

**Claude cannot find Figma tools**
→ Check `claude_desktop_config.json`. Make sure the path to `dist/local.js` is the full absolute path. Restart Claude Desktop.

**Plugin does not appear in Figma**
→ Go to `Plugins → Development → Refresh plugins`.

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

## Links

- [Report Issues](https://github.com/southleft/figma-console-mcp/issues)
- [Discussions](https://github.com/southleft/figma-console-mcp/discussions)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [Figma API](https://www.figma.com/developers/api)
