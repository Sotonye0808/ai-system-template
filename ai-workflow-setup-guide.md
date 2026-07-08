# AI-Assisted Development — Setup Guide

## A Complete Guide to the `ai-system` Framework

---

## What This Guide Sets Up

This guide walks you through integrating the `ai-system` AI-assisted development framework into your project. It works with any AI coding tool — the framework is entirely vendor-neutral.

When you're done, you'll have:

- **A structured project documentation system** that keeps AI agents in sync with your codebase
- **Protocol-driven agent behavior** — entry protocol, context tiering, quality gates, escalation rules
- **Command-driven workflows** — feature pipelines, daily dev cycles, interruption recovery, quality verification
- **Interruption-safe state tracking** — restart after crashes or context resets without losing progress
- **A quality gate** that prevents overconfident output, scope-padding, and solutions overfit to examples

> **Setup takes roughly 10–15 minutes.** Everything is file-based — no external services or API keys required for the framework itself.

### Who This Is For

- Developers using any AI coding tool who want consistent, governed development workflows
- Teams that want AI agents to follow the same protocols regardless of which tool each developer uses
- Anyone who has experienced an AI agent confidently doing the wrong thing and wants systematic quality checks

---

## Before You Begin

### Requirements

| Requirement          | Details                                                       |
| -------------------- | ------------------------------------------------------------- |
| A project repository | Any language, any framework                                   |
| An AI coding tool    | Any tool that can read Markdown files and follow instructions |
| `ai-system` kit      | Copy from `ai-system-kit/` in this repo                       |

---

## Step 1 — Copy the `ai-system` Kit

Copy the entire kit into your project root:

```powershell
Copy-Item -Recurse -Path 'path\to\ai-system-kit\ai-system' -Destination '.\' -Force
Copy-Item 'path\to\ai-system-kit\ai-context.md' '.\ai-context.md' -Force
```

---

## Step 2 — Run the Bootstrap Command

In your AI tool's chat, run:

```
Execute command: bootstrap-project.md
Directive: [describe your project — e.g., "Next.js + Node.js marketplace app"]
```

The AI will analyze your codebase and populate all `ai-system` files with project-specific content.

---

## Step 3 — Understand the System

### How It Works

1. **Every session starts** by reading `ai-context.md` → `protocols/entry-protocol.md`
2. **The entry protocol** tells the agent which files to read based on context budget (context-tiering)
3. **The agent picks a role** — Planner, Architect, Implementer, Reviewer, Tester/QA, or Historian — based on the task
4. **Commands are executed** via the `Execute command: [file].md` pattern
5. **Every command runs the quality gate** before marking work complete

### Key Concepts

- **Context Tiering:** A "always read" core (~1.5k tokens) plus progressive disclosure — small models get what they need, large models don't waste context
- **Quality Gate:** 9 mandatory checks (requirement match, generalization, scope, architecture, assumptions, error paths, verification, re-prompt debt, pattern adherence)
- **Interruption Safety:** `checkpoints/in-progress.md` tracks half-done work; `resume-session.md` recovers from any interruption
- **Freshness Metadata:** Every doc tracks when it was last verified against the actual code, so agents know when to re-derive vs. trust

---

## Daily Workflow

### Starting a Session

1. Open your project
2. In your AI tool, start with:
   ```
   Read ai-context.md before responding.
   ```
3. The agent follows the entry protocol to orient itself
4. Run the daily cycle:
   ```
   Execute command: dev-cycle.md
   ```

### Common Tasks

| What You Want to Do                    | How to Do It                                                             |
| -------------------------------------- | ------------------------------------------------------------------------ |
| Plan a new feature before writing code | `Execute command: plan-feature.md` + `Directive: describe your feature`  |
| Implement a full feature end-to-end    | `Execute command: execute-feature.md` + `Directive: feature description` |
| Run daily development                  | `Execute command: dev-cycle.md`                                          |
| Fix a build or test failure            | `Execute command: fix-build.md` + `Directive: paste the error`           |
| Verify current work quality            | `Execute command: verify-work.md`                                        |
| Sync docs with code mid-sprint         | `Execute command: sync-context.md`                                       |
| Check for architecture drift           | `Execute command: audit-drift.md`                                        |
| Resume after interruption              | `Execute command: resume-session.md`                                     |

---

## Troubleshooting

| Problem                             | Solution                                                             |
| ----------------------------------- | -------------------------------------------------------------------- |
| Agent ignores the entry protocol    | Prepend: `Read protocols/entry-protocol.md before responding`        |
| Agent produces overfit code         | Run `verify-work.md` with `Directive: Focus on generalization check` |
| Agent modifies files outside scope  | Check scope discipline criterion in quality gate — revert and re-run |
| Docs get stale during long sessions | Run `sync-context.md` at regular intervals                           |
| Session interrupted mid-work        | Run `resume-session.md` — checkpoint state is preserved              |
| Architecture drift detected         | Run `audit-drift.md` to generate a discrepancy report                |
| Bootstrap generates generic content | Add a detailed directive describing your stack and goals             |
