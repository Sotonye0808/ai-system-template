# AI Dev Workflow — Personal Setup & Reference Guide

> Your personal reference for the VS Code AI-assisted development workflow built around Continue, Cline, Supermaven, Sourcegraph Amp, codebase-mcp, and Ollama. Everything you need to remember is in this document.

---

## Your Full Stack

### VS Code Extensions

| Extension       | Role                                  | Key Action               |
| --------------- | ------------------------------------- | ------------------------ |
| Supermaven      | Instant autocomplete (always on)      | Tab to accept suggestion |
| Continue        | AI reasoning, planning, code chat     | Ctrl+I for inline edit   |
| Cline           | Autonomous multi-file coding agent    | Open panel → give task   |
| Sourcegraph Amp | Repository intelligence + code search | `Amp: Index Repository`  |

### Model Routing

| Role                  | Model               | Provider       | Use When                                 |
| --------------------- | ------------------- | -------------- | ---------------------------------------- |
| Planner / Architect   | DeepSeek Coder V2   | Together AI    | Architecture, large refactors, reasoning |
| Autocomplete          | Qwen 2.5 Coder 32B  | Together AI    | Inline suggestions (always background)   |
| Reviewer / Quick Chat | Llama 3 70B         | Groq           | Quick questions, code review             |
| Offline Fallback      | qwen2.5-coder:7b    | Ollama (local) | No internet / API limit hit              |
| Offline Coder         | deepseek-coder:6.7b | Ollama (local) | Apply/edit offline                       |
| Offline Debug         | codellama:7b        | Ollama (local) | Debugging offline                        |

> **TIP:** Continue automatically routes to local Ollama models when cloud models fail. Supermaven always runs locally so autocomplete never stops.

---

## Starting a Coding Session

### Step 1 — Launch the Environment

Run `start-ai-dev.bat` from your project folder. This starts Ollama and codebase-mcp automatically, then opens VS Code.

```bat
start-ai-dev.bat
```

Or manually in PowerShell:

```powershell
ollama serve
codebase-mcp start
code .
```

### Step 2 — Bootstrap a New Project (First Time Only)

Open Continue chat and run the bootstrap command. This generates all `.ai-system` files from your actual codebase.

```
Execute command: .ai-system/commands/bootstrap-project.md
```

With an optional directive:

```
Execute command: bootstrap-project.md
Directive: This is a Next.js marketplace — focus on modular service architecture
```

### Step 3 — Start the Dev Cycle (Every Session)

```
Execute command: .ai-system/commands/dev-cycle.md
```

The agent will read the task queue, plan, implement, review, test, and document — one task at a time.

---

## AI Command Reference

### Keyboard Shortcuts (set these in VS Code)

| Shortcut   | Command File             | What It Does                                     |
| ---------- | ------------------------ | ------------------------------------------------ |
| Ctrl+Alt+B | bootstrap-project.md     | Initialize or refresh .ai-system for this repo   |
| Ctrl+Alt+D | dev-cycle.md             | Run full plan → implement → test → document loop |
| Ctrl+Alt+P | plan-feature.md          | Plan a new feature (no code written yet)         |
| Ctrl+Alt+R | refactor-codebase.md     | Analyze and improve code structure               |
| Ctrl+Alt+F | fix-build.md             | Self-healing error diagnosis and fix loop        |
| Ctrl+Alt+A | generate-architecture.md | Document or redesign system architecture         |

### How to Run Commands

In Continue chat:

```
Execute command: .ai-system/commands/[command-name].md
```

With an optional directive:

```
Execute command: plan-feature.md
Directive: implement JWT authentication with refresh tokens
```

### Multi-Agent Pattern

| Agent            | Tool     | Prompt Example                                                          |
| ---------------- | -------- | ----------------------------------------------------------------------- |
| Planner          | Continue | `Analyze task-queue.md and determine next development step`             |
| Architect        | Continue | `Update system-architecture.md to support the next task`                |
| Coder / Executor | Cline    | `Implement the architecture described in system-architecture.md`        |
| Reviewer         | Continue | `Review the last changes for architecture consistency and code quality` |
| Tester           | Cline    | `Run the self-healing test loop from self-heal.md`                      |

---

## .ai-system File Reference

The `.ai-system` directory is the AI brain of your project. Every file has a specific job.

| File                             | Purpose                                          | Updated By                                 |
| -------------------------------- | ------------------------------------------------ | ------------------------------------------ |
| `.ai-context.md`                 | Project overview — first thing every agent reads | You / Bootstrap                            |
| `agents/general-instructions.md` | Master agent protocol and principles             | You                                        |
| `agents/system-architecture.md`  | How the system is structured                     | Architect agent / generate-architecture.md |
| `agents/project-context.md`      | Project goals, users, constraints                | You / Bootstrap                            |
| `agents/design-system.md`        | UI/UX rules and component patterns               | You / Bootstrap                            |
| `agents/repair-system.md`        | Known errors and how to fix/avoid them           | fix-build.md / self-heal.md                |
| `planning/project-plan.md`       | High-level feature checklist                     | You / agents                               |
| `planning/task-queue.md`         | Sprint tasks (agents work top to bottom)         | dev-cycle.md / you                         |
| `checkpoints/session-log.md`     | What was done, what's next                       | All agents after tasks                     |
| `memory/project-decisions.md`    | Why architecture decisions were made             | Architect agent                            |
| `memory/lessons-learned.md`      | Development process insights                     | update-ai-system.md                        |
| `index/repo-map.md`              | Folder map with descriptions                     | Bootstrap / update-ai-system.md            |
| `index/dependency-graph.md`      | Module relationships                             | Bootstrap / update-ai-system.md            |
| `index/file-summaries/`          | Short summaries of each major file               | Bootstrap / update-ai-system.md            |
| `summaries/dev-history.md`       | Chronological log of completed work              | Agents at sprint end                       |
| `testing/test-results.md`        | Latest test run results                          | self-heal.md                               |
| `testing/repair-system.md`       | Error knowledge base                             | fix-build.md                               |

---

## Cline Configuration

### Primary Model (OpenRouter)

| Setting      | Value                          |
| ------------ | ------------------------------ |
| API Provider | OpenAI Compatible              |
| Base URL     | `https://openrouter.ai/api/v1` |
| API Key      | Your OpenRouter API key        |
| Model        | `qwen/qwen3-coder:free`        |

### Offline Fallback (Ollama)

| Setting      | Value                    |
| ------------ | ------------------------ |
| API Provider | Ollama                   |
| Endpoint     | `http://localhost:11434` |
| Model        | `qwen2.5-coder:7b`       |

---

## Ollama — Local Model Management

### Install & Pull Models (run once)

```powershell
ollama pull qwen2.5-coder:7b
ollama pull deepseek-coder:6.7b
ollama pull codellama:7b
```

### Start Manually (if not auto-starting)

```powershell
ollama serve
```

### Check Available Models

```powershell
ollama list
```

### Auto-Start on Windows

Go to **Settings → Apps → Startup Apps** and ensure Ollama is enabled. If not, add to Windows startup:

```
C:\Users\%USERNAME%\AppData\Local\Programs\Ollama\ollama.exe
```

---

## codebase-mcp

### Verify Installation

```powershell
codebase-mcp version
where codebase-mcp
```

### MCP Config Location (global)

```
C:\Users\%USERNAME%\.mcp\config.json
```

Contents — use the absolute path since you're on nvm4w:

```json
{
  "providers": {
    "codebase": {
      "command": "C:\\nvm4w\\nodejs\\codebase-mcp.cmd"
    }
  }
}
```

### Verify in VS Code

Open Command Palette (`Ctrl+Shift+P`) and type:

```
MCP: List Providers
```

Expected output: `filesystem`, `git`, `codebase`

> **NOTE:** `MCP: List Providers` is a VS Code command palette action — do NOT run it in the terminal.

---

## Continue Config (YAML)

Save this to `~/.continue/config.yaml` — replace the placeholder API keys:

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

## PowerShell Quick Commands

### Create Full .ai-system Structure

```powershell
# Run from your project root
$dirs = @(
  ".ai-system/agents",
  ".ai-system/planning",
  ".ai-system/commands",
  ".ai-system/checkpoints",
  ".ai-system/memory",
  ".ai-system/index/file-summaries",
  ".ai-system/abstract",
  ".ai-system/summaries",
  ".ai-system/testing",
  ".ai-system/docs"
)
foreach ($d in $dirs) { New-Item -ItemType Directory -Force -Path $d }
```

### Copy Starter Kit Into a New Project

```powershell
# Copy entire .ai-system kit from your template folder
Copy-Item -Recurse -Path 'C:\path\to\ai-system-kit\.ai-system' -Destination '.\' -Force
Copy-Item 'C:\path\to\ai-system-kit\.ai-context.md' '.\.ai-context.md' -Force
```

### Check Ollama Status

```powershell
Invoke-RestMethod http://localhost:11434/api/tags | ConvertTo-Json
```

---

## Tips, Tricks & Edge Cases

### Performance Tips (8GB RAM)

- Always use cloud models (Together / Groq) for large tasks — local models are fallback only
- Keep `file-summaries` in `.ai-system/index` updated so agents don't re-scan full files
- Disable VS Code minimap and follow-symlinks for better performance:
  ```json
  {
    "editor.minimap.enabled": false,
    "search.followSymlinks": false,
    "files.autoSave": "onFocusChange",
    "extensions.autoUpdate": false
  }
  ```
- Close unused browser tabs while running Cline on large tasks — it's memory intensive
- If VS Code feels slow, close the Amp panel — it has background indexing

### Prompt Tips

- Always start sessions with: `Read .ai-context.md before answering`
- Use directives to refine commands without rewriting them
- For large refactors, use the Planner → Architect → Cline multi-agent pattern
- If an agent drifts off-task: `Check task-queue.md and continue from where you left off`
- Cline is better than Continue for anything that modifies multiple files
- Amp is best for finding where something is implemented before making changes

### When Things Go Wrong

| Problem                                   | Solution                                                       |
| ----------------------------------------- | -------------------------------------------------------------- |
| Continue not connecting to Together AI    | Check API key in config.yaml — no trailing spaces              |
| codebase-mcp not showing in MCP providers | Restart VS Code; check `.mcp/config.json` uses absolute path   |
| Ollama models not loading                 | Run `ollama serve` — check `ollama list`                       |
| Cline making too many unrelated changes   | Use more specific prompts; add `Directive: one file at a time` |
| Agent ignoring architecture docs          | Prepend: `Read system-architecture.md before responding`       |
| Rate limit on Together AI / Groq          | Continue falls back to Ollama automatically                    |
| Bootstrap generates generic content       | Add a detailed directive describing your stack and goals       |
| Supermaven conflicts with autocomplete    | Ensure GitHub Copilot is disabled                              |

### Copilot Compatibility

This workflow coexists with GitHub Copilot if your allowance is renewed:

- **Copilot active:** Disable Supermaven autocomplete in its settings panel
- **Supermaven active:** Disable GitHub Copilot in Extensions
- Continue + Cline work alongside both — they operate at a higher level than inline completion

### API Keys Reference

| Service               | URL                      | Free Tier                            |
| --------------------- | ------------------------ | ------------------------------------ |
| Together AI           | https://api.together.xyz | Free monthly credits, no card needed |
| Groq                  | https://console.groq.com | Free tier, no card needed            |
| OpenRouter (optional) | https://openrouter.ai    | Requires card for paid models        |
