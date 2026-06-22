---
name: google-agent-cli
description: Mastering Google Antigravity CLI (agy) operations, including configuration settings, keyboard shortcuts, TUI interactive commands, and sandbox configurations.
---

# 💻 Google Antigravity CLI (agy) Mastery Skill

Use this skill when you need to configure, debug, or optimize the interactive terminal-based experience of the **Google Antigravity CLI (`agy`)**.

---

## 1. Getting Started & Installation

The **`agents-cli`** is a unified command-line tool for the full agent development lifecycle.

### 📋 Prerequisites
*   **Python 3.11+**
*   **uv** (recommended Python package manager)
*   **Node.js** (for skill installation and registry resolving)
*   **gcloud SDK** (optional, for Cloud credential forwarding)

### 🚀 Quick Setup
The recommended method to install `agents-cli` globally and add default agent skills is:
```bash
uvx google-agents-cli setup
```
This registers the core CLI and deploys the framework-specific skills under `~/.agents/skills/`.

#### Alternative Installation:
*   To install the CLI as a standalone global tool:
    ```bash
    uv tool install google-agents-cli
    ```
*   To only bootstrap development skills inside your active project:
    ```bash
    npx skills add google/agents-cli
    ```

---

## 2. Quick Keyboard & Surface Shortcuts

Ensure rapid and seamless navigation across the terminal-based user interface (TUI):

*   **`Ctrl + D` (Twice):** Exits the active session safely.
*   **`ESC`:** Closes active overlays (like `/skills`, `/settings`, or `/context`).
*   **`Up/Down Arrow Keys`:** Browses past command history.

---

## 3. CLI Interactive Commands

The `agy` environment includes specific TUI commands to manage developer sessions, list context, and configure live run modes:

| Command | Description |
| :--- | :--- |
| `/context` | Inspect all files, symbols, and directories loaded into the agent's current token window. |
| `/diff` | Render a line-by-line visual difference of modified files across the project workspace. |
| `/fork` | Create a copy of the current active session in a new thread, preserving history. |
| `/new` | Purges the current workspace buffer and restarts with a clean thread context. |
| `/permissions` | Reviews and adjusts execution permissions (allow/deny lists) for commands, URLs, and files. |
| `/settings` | Launches an interactive graphical setting menu to configure CLI features directly. |

---

## 4. Configuration Management (`settings.json`)

The global configuration is stored under `~/.gemini/antigravity-cli/settings.json`.

### 🛠️ Common Configuration Keys & Adjustments:
```json
{
  "allowNonWorkspaceAccess": false,
  "artifactReviewPolicy": "asks-for-review",
  "enableTerminalSandbox": true,
  "historySize": 2000,
  "model": "gemini-3.5-flash",
  "toolPermission": "request-review",
  "verbosity": "high"
}
```

### 🔒 Sandbox Security Configurations:
To run arbitrary CLI commands safely inside a restricted environment:
*   Set `"enableTerminalSandbox": true` to isolate network, folder access, and executable scopes during agent tool calls.
