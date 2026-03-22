# Figma Console MCP Server

[![MCP](https://img.shields.io/badge/MCP-Compatible-blue)](https://modelcontextprotocol.io/)
[![npm](https://img.shields.io/npm/v/figma-console-mcp)](https://www.npmjs.com/package/figma-console-mcp)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Sponsor](https://img.shields.io/badge/Sponsor-southleft-ea4aaa?logo=github-sponsors&logoColor=white)](https://github.com/sponsors/southleft)

> Connect **Claude Desktop** to **Figma Desktop** and design, inspect, and manage your files through natural language.

---

## What is this?

This connects Claude to your Figma file so you can design with prompts. Just describe what you want — Claude will create it directly in Figma.

- Create UI components and full screens by typing what you need
- Integrate and manage your design system (tokens, variables, styles)
- Inspect, audit, and debug your Figma files through conversation

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

Open your terminal and check if Node.js is installed:

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

Open your terminal and run:

```bash
git clone https://github.com/AbhinavAgr/figma-claude-MCP.git
```
### then
```bash
cd figma-claude-mcp
```
---

### Step 2 — Install Dependencies

In the same terminal, run:

```bash
npm install
```

---

### Step 3 — Build the Local MCP Server

Then run:

```bash
npm run build:local
```

This generates `dist/local.js` — the file Claude Desktop will run.

---

### Step 4 — Get Your Figma API Token

1. Go to **Manage personal access tokens** in Figma:
   - In Figma Desktop: click your profile icon → **Settings** → **Security** → **Personal access tokens**
   - Or visit [Figma Help: Personal access tokens](https://help.figma.com/hc/en-us/articles/8085703771159) and follow the steps
2. Click **Add new token**
3. Enter name: `Figma Claude MCP`
4. Copy the token — you won't see it again! (starts with `figd_`)

---

### Step 5 — Configure Claude Desktop

Open the Claude Desktop config folder in your terminal:

**macOS:**
```bash
open ~/Library/Application\ Support/Claude/
```

**Windows** — press `Win + R` and enter:
```
%APPDATA%\Claude\
```

Create (or edit) `claude_desktop_config.json` inside that folder:

```json
{
  "mcpServers": {
    "figma-console": {
      "command": "node",
      "args": [
        "/Users/YOUR_USERNAME/figma-claude-mcp/dist/local.js"
      ],
      "env": {
        "FIGMA_ACCESS_TOKEN": "YOUR_FIGMA_TOKEN"
      }
    }
  }
}
```

Replace `YOUR_USERNAME` with your actual system username and `YOUR_FIGMA_TOKEN` with the token from Step 4.

> **Tip:** The path in `args` must be the full absolute path to `dist/local.js`. Run `pwd` inside the `figma-console-mcp` folder to get it.

Final code should look something like this (make sure {} are properly placed)

```json
{
  "preferences": {
    "coworkScheduledTasksEnabled": true,
    "ccdScheduledTasksEnabled": true,
    "coworkWebSearchEnabled": true,
    "sidebarMode": "chat"
  },
  "mcpServers": {
    "figma-console": {
      "command": "node",
      "args": [
        "/Users/abhinav/figma-claude-mcp/dist/local.js"
      ],
      "env": {
        "FIGMA_ACCESS_TOKEN": "YOUR_FIGMA_TOKEN"
      }
    }
  }
}
```

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

In **Claude Desktop**, start with:

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

## Troubleshooting

**Plugin shows "connecting" and never reaches MCP ready**
→ Restart Claude Desktop, then re-run the plugin in Figma.

**Claude cannot find Figma tools**
→ Check `claude_desktop_config.json`. Make sure the path to `dist/local.js` is the full absolute path. Restart Claude Desktop.

**Plugin does not appear in Figma**
→ Go to `Plugins → Development → Refresh plugins`.

---

## Example Prompts

Once connected, try these in Claude Desktop to create screens directly in Figma:

**Login Screen**
```
Create a mobile login screen with an app logo at the top, email and password fields, a Sign In button, and a "Forgot password?" link below
```

**Dashboard**
```
Design a web dashboard with a sidebar navigation, a header with a search bar and user avatar, and a main area showing 4 stat cards and a recent activity table
```

**Onboarding Flow**
```
Build a 3-step onboarding screen with a progress indicator at the top, an illustration area, a title, a short description, and Next/Back buttons at the bottom
```

---

## License

MIT — see [LICENSE](LICENSE) for details.

For more visit [abhinavagr.com](https://abhinavagr.com/)

---

> **Note:** This is a beginner-friendly adaptation of the [original Figma Console MCP repository](https://github.com/southleft/figma-console-mcp/tree/main?tab=readme-ov-file). I've simplified the setup guide and focused it on Claude Desktop so it's easier to get started. If you want the full documentation — including NPX setup, Cloud Mode, Remote SSE, and advanced options — visit the original repo.
