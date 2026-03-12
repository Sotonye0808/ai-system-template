# Free AI Coding Workflow in VS Code

## A Complete Setup Guide — No Copilot Subscription Required

---

## What This Guide Sets Up

This guide walks you through building a powerful AI-assisted development environment inside Visual Studio Code — completely free, no credit card required.

When you're done, you'll have:

- **Copilot-style autocomplete** (Supermaven — free tier)
- **AI chat + code editing assistant** (Continue + free model APIs)
- **An autonomous multi-file coding agent** (Cline)
- **Repository intelligence and code search** (Sourcegraph Amp)
- **Offline fallback using local models** (Ollama)
- **A structured project documentation system** that keeps AI agents in sync with your codebase

> **Note:** Setup takes roughly 20–30 minutes. Everything runs within the free tiers of the API providers used.

### Who This Is For

This setup suits developers who:

- Want a GitHub Copilot alternative without a monthly subscription
- Work on mid-to-large codebases and want AI to understand the whole project
- Want an autonomous coding agent that can execute multi-file changes
- Need to work offline sometimes and want local model fallback
- Are running a modest machine (8–16GB RAM) and can't run large local models

---

## Before You Begin

### Requirements

| Requirement              | Details                                                                     |
| ------------------------ | --------------------------------------------------------------------------- |
| VS Code                  | Any recent version — [code.visualstudio.com](https://code.visualstudio.com) |
| Node.js v18+             | [nodejs.org](https://nodejs.org) — used by codebase-mcp                     |
| Internet connection      | For initial setup and cloud model API calls                                 |
| GitHub or Google account | For signing up to free API services                                         |

> **No credit card is required for any part of this setup.** Together AI and Groq both offer free tiers without billing information.

---

## Step 1 — Create Free API Keys

These are the model providers that power your AI tools. Both are completely free with no card required.

### Together AI (Primary Models)

Together AI hosts powerful open-source coding models including DeepSeek and Qwen.

1. Go to: [https://api.together.xyz](https://api.together.xyz)
2. Sign in with GitHub or Google
3. Click **API Keys** in the left sidebar
4. Click **Create new key** — give it any name
5. Copy the key and save it somewhere safe

> Together AI gives you free monthly credits automatically on signup. No billing setup needed.

### Groq (Fast Backup Models)

Groq is extremely fast and great for quick questions and code review.

1. Go to: [https://console.groq.com](https://console.groq.com)
2. Sign in with GitHub or Google
3. Click **API Keys**
4. Create a new key and copy it

---

## Step 2 — Install VS Code Extensions

Open VS Code. For each extension below, press `Ctrl+Shift+X` to open the Extensions panel, search by name, and click **Install**.

| Extension Name  | Search For               | Purpose                                       |
| --------------- | ------------------------ | --------------------------------------------- |
| Supermaven      | `Supermaven`             | Fast inline code autocomplete (Copilot-style) |
| Continue        | `Continue.continue`      | AI coding assistant — chat, edit, refactor    |
| Cline           | `saoudrizwan.claude-dev` | Autonomous coding agent — multi-file changes  |
| Sourcegraph Amp | `Sourcegraph Amp`        | Repository intelligence and code search       |

### After Installing

- Reload VS Code (`Ctrl+Shift+P` → `Developer: Reload Window`)
- Sign into **Supermaven** using the prompt that appears — use GitHub or Google, no payment needed
- If you have **GitHub Copilot** installed, disable it to prevent autocomplete conflicts: Extensions → GitHub Copilot → Disable

---

## Step 3 — Configure Continue

Continue is the main AI assistant. You need to tell it which models to use.

### Open the Config File

1. Press `Ctrl+Shift+P`
2. Type `Continue: Open Config` and press Enter
3. The config file opens — replace its entire contents with the configuration below

### Config File

Replace `YOUR_OPENROUTER_KEY`, `YOUR_GROQ_KEY`, and `YOUR_GOOGLE_AI_STUDIO_KEY` with your actual keys:

```yaml
name: AI Dev Stack
version: 1.0.0
schema: v1

models:
  # PRIMARY — Best free coding model (OpenRouter native provider)
  - name: Qwen3 Coder
    provider: openrouter
    model: qwen/qwen3-coder:free
    apiKey: YOUR_OPENROUTER_KEY
    roles:
      - chat
      - edit
      - apply
    defaultCompletionOptions:
      temperature: 0.4
      maxTokens: 2000

  # REASONING — DeepSeek free via OpenRouter
  - name: Devstral Coder
    provider: openrouter
    model: mistralai/devstral-2512:free:free
    apiKey: YOUR_OPENROUTER_KEY
    roles:
      - chat
    defaultCompletionOptions:
      temperature: 0.5
      maxTokens: 2000

  # FAST CHAT — Groq (fixed model ID)
  - name: Llama Groq
    provider: openai
    apiBase: https://api.groq.com/openai/v1
    model: llama-3.3-70b-versatile
    apiKey: YOUR_GROQ_KEY
    roles:
      - chat
      - autocomplete
    autocompleteOptions:
      debounceDelay: 200
      maxPromptTokens: 1200
      onlyMyCode: true
    defaultCompletionOptions:
      temperature: 0.6
      maxTokens: 1500

  # ─── LOCAL FALLBACK MODELS (Ollama — work offline, no API key needed) ─────────

  # MAIN LOCAL FALLBACK — mirrors Qwen cloud model
  - name: Ollama Qwen (offline)
    provider: ollama
    model: qwen2.5-coder:7b
    roles:
      - chat
      - edit

  # LOCAL CODER FALLBACK — mirrors DeepSeek cloud model
  - name: Ollama DeepSeek (offline)
    provider: ollama
    model: deepseek-coder:6.7b
    roles:
      - apply
      - autocomplete

  # LOCAL DEBUGGER FALLBACK
  - name: Ollama CodeLlama (offline)
    provider: ollama
    model: codellama:7b
    roles:
      - chat
```

---

## Step 4 — Configure Cline

Cline is the autonomous coding agent. It needs to know which model to use.

1. Click the **Cline icon** in the VS Code sidebar
2. Click the settings/gear icon
3. Enter the following settings:

| Setting      | Value                          |
| ------------ | ------------------------------ |
| API Provider | OpenAI Compatible              |
| Base URL     | `https://openrouter.ai/api/v1` |
| API Key      | Your OpenRouter API key        |
| Model        | `qwen/qwen3-coder:free`        |

---

## Step 5 — Install Ollama (Offline Fallback)

Ollama lets you run AI models locally on your computer. This is your backup for when internet is unavailable or API rate limits are hit.

### Install

1. Go to: [https://ollama.com/download](https://ollama.com/download)
2. Download and run the installer for your operating system
3. Once installed, open a terminal and run these commands to download models:

```bash
ollama pull qwen2.5-coder:7b
ollama pull deepseek-coder:6.7b
ollama pull codellama:7b
```

> These downloads are around 4–6GB total. Run them on a good connection. The 7B models work well on machines with 8GB RAM.

### Add Ollama to Cline (Offline Fallback)

In Cline settings, you can configure Ollama as a fallback provider:

| Setting      | Value                    |
| ------------ | ------------------------ |
| API Provider | Ollama                   |
| Base URL     | `http://localhost:11434` |
| Model        | `qwen2.5-coder:7b`       |

---

## Step 6 — Install codebase-mcp

codebase-mcp gives AI agents structured access to your repository — they can understand folder structure, module dependencies, and navigate code without you having to paste files into every prompt.

### Install

```bash
npm install -g @iflow-mcp/codebase-mcp
```

### Verify

```bash
codebase-mcp version
```

If you get "command not found", check that your npm global bin directory is in your system PATH.

### Create the Config File

Create the folder and file at:

- **Windows:** `C:\Users\[YourUsername]\.mcp\config.json`
- **Mac/Linux:** `~/.mcp/config.json`

File contents:

```json
{
  "providers": {
    "codebase": {
      "command": "codebase-mcp"
    }
  }
}
```

**Windows with nvm** — use the absolute path instead:

```json
{
  "providers": {
    "codebase": {
      "command": "C:\\nvm4w\\nodejs\\codebase-mcp.cmd"
    }
  }
}
```

After creating the config, **restart VS Code completely**. Then press `Ctrl+Shift+P` and type `MCP: List Providers` — you should see `codebase` in the list.

---

## Step 7 — Create a Startup Script (Optional but Recommended)

Create a file that starts Ollama and codebase-mcp automatically when you begin a coding session.

### Windows (`start-ai-dev.bat`)

```bat
@echo off
start "" "C:\Users\%USERNAME%\AppData\Local\Programs\Ollama\ollama.exe" serve
timeout /t 3 /nobreak >nul
start "" cmd /c "codebase-mcp start"
code .
```

### Mac/Linux (`start-ai-dev.sh`)

```bash
#!/bin/bash
ollama serve &
sleep 2
codebase-mcp start &
code .
```

Run this script at the start of every coding session to bring everything online automatically.

---

## Step 8 — Set Up the .ai-system Project Structure

The `.ai-system` folder is where all AI agent documentation lives. It keeps your AI tools informed about the project so they give better, more consistent results over time.

### Create the Structure

Run this from your project root:

**Windows PowerShell:**

```powershell
$dirs = @(
  ".ai-system/agents",
  ".ai-system/planning",
  ".ai-system/commands",
  ".ai-system/checkpoints",
  ".ai-system/memory",
  ".ai-system/index/file-summaries",
  ".ai-system/summaries",
  ".ai-system/testing"
)
foreach ($d in $dirs) { New-Item -ItemType Directory -Force -Path $d }
```

**Mac/Linux:**

```bash
mkdir -p .ai-system/{agents,planning,commands,checkpoints,memory,index/file-summaries,summaries,testing}
```

### Bootstrap the Project (AI-Generated Content)

Open Continue chat (`Ctrl+I` or the Continue sidebar) and paste this prompt:

```
You are an AI development orchestrator.
Analyze this repository and generate all .ai-system documentation files.
Each file must start with a short overview summary.
Create:
- .ai-context.md (project overview, stack, key modules)
- .ai-system/agents/system-architecture.md
- .ai-system/agents/project-context.md
- .ai-system/agents/design-system.md
- .ai-system/agents/repair-system.md
- .ai-system/planning/project-plan.md
- .ai-system/planning/task-queue.md
- .ai-system/index/repo-map.md
- .ai-system/index/dependency-graph.md
```

The AI will analyze your codebase and populate all files with project-specific content.

> **Tip:** Add a directive to get better results: append `Directive: This is a Next.js + Node.js app. Focus on modular service architecture.` with details about your own project.

---

## Daily Workflow

### Starting a Session

1. Run `start-ai-dev.bat` (or start services manually)
2. Open your project in VS Code
3. In Continue chat, run:
   ```
   Execute command: .ai-system/commands/dev-cycle.md
   ```
4. The AI reads the task queue, plans, implements, tests, and documents

### Common Tasks

| What You Want to Do                       | How to Do It                                                                         |
| ----------------------------------------- | ------------------------------------------------------------------------------------ |
| Plan a new feature before writing code    | In Continue: `Execute command: plan-feature.md` + `Directive: describe your feature` |
| Refactor your codebase                    | In Continue: `Execute command: refactor-codebase.md` + `Directive: your goal`        |
| Fix a broken build or error               | In Continue: `Execute command: fix-build.md` + `Directive: paste the error`          |
| Implement something across multiple files | In Cline: describe the task, reference `system-architecture.md`                      |
| Update AI docs after major changes        | In Continue: `Execute command: update-ai-system.md`                                  |
| Find where something is implemented       | In Amp: `@amp find [what you're looking for]`                                        |

---

## When to Use Each Tool

| Tool            | Best For                                             | Not Good For                                 |
| --------------- | ---------------------------------------------------- | -------------------------------------------- |
| Supermaven      | Instant inline completion while typing               | Large reasoning tasks                        |
| Continue        | Planning, architecture, reasoning, quick edits       | Executing changes across many files          |
| Cline           | Multi-file changes, autonomous tasks, refactors      | Quick questions or planning                  |
| Sourcegraph Amp | Finding code, tracing calls, understanding structure | Making changes                               |
| Ollama          | Offline work, when API limits are hit                | Complex reasoning (local models are smaller) |

---

## Troubleshooting

| Problem                                        | Solution                                                              |
| ---------------------------------------------- | --------------------------------------------------------------------- |
| Continue shows error connecting to Together AI | Check your API key in the config file — no extra spaces or quotes     |
| codebase-mcp not listed in MCP providers       | Restart VS Code; verify `.mcp/config.json` exists at the correct path |
| Ollama models not found                        | Run `ollama serve`, then `ollama list` — pull the model if missing    |
| Supermaven not suggesting anything             | Ensure GitHub Copilot is disabled in Extensions                       |
| Cline keeps editing unrelated files            | Add to your prompt: `only modify files related to [feature]`          |
| AI generates generic architecture docs         | Add a detailed directive when bootstrapping                           |
| API rate limit hit                             | Continue automatically falls back to Ollama local models              |
| `node` not found / `codebase-mcp` not found    | Ensure Node.js v18+ is installed and npm global bin is in PATH        |

---

## Free Tier Limits

| Provider    | Free Tier                                  | What Happens at Limit                         |
| ----------- | ------------------------------------------ | --------------------------------------------- |
| Together AI | Free monthly credits on signup             | Requests fail — Continue falls back to Ollama |
| Groq        | Free usage with rate limits                | Slight delay or fallback to next model        |
| Supermaven  | Free tier with unlimited basic completions | No limits on basic usage                      |
| Ollama      | Fully free — runs on your machine          | Limited by your RAM / CPU speed               |

> For most solo developers, the free tiers on Together AI and Groq are more than enough. If you hit limits regularly, adding $5–10 credit to Together AI gives a very long runway at low cost.

---

## You're Set Up

Your AI-assisted development environment is ready. Here's what you have:

- **Supermaven** — always-on autocomplete as you type
- **Continue** — your AI coding assistant for planning, reasoning, and edits
- **Cline** — your autonomous agent for multi-file changes and complex tasks
- **Sourcegraph Amp** — intelligent code search and repo understanding
- **codebase-mcp** — structured context provider so agents understand your codebase
- **Ollama** — local fallback models so you can code offline
- **.ai-system directory** — project brain that keeps all AI tools aligned

Start every new project by running the **bootstrap command** in Continue, and use the **dev-cycle command** to drive your daily work. The more you keep the `.ai-system` files updated, the smarter your AI tools become over time.
