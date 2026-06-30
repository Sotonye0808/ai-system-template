# AI-Assisted Development System (`.ai-system`)

## Overview

A vendor-neutral, model-agnostic framework for AI-assisted software development. Provides structured documentation, command-driven workflows, and quality gates that work identically across any AI coding tool — CLI, IDE extension, API loop, or autonomous agent.

## Philosophy

- **No lock-in.** Roles are functions, not tools. No file names a specific AI product, vendor, or model.
- **Context-budget resilience.** A tiered reading protocol means a 200k-context frontier model and a small fast model both get what they need — one from progressive disclosure, the other from the minimal always-read core.
- **Closed-loop quality.** Every command runs through a mandatory QA gate before declaring work complete. No "should work" handoffs.
- **Interruption-safe.** Checkpoint tracking means any session can be resumed after a crash, reset, or context switch.
- **File-based state.** No external database — everything is Markdown with structured metadata, works in any repo with any toolchain.
- **Pattern discipline.** Config-driven, fallback-safe, single-source-of-truth engineering standards are enforced through the quality gate, not left as unstated convention.

## Directory Contract

```
.ai-system/
├── protocols/          # How any agent behaves (entry, tiering, QA, escalation, verification)
├── agents/             # Role definitions (function-based: Planner, Architect, Implementer, etc.)
├── commands/           # Reusable command pipelines (execute-feature, dev-cycle, fix-build, etc.)
├── standards/          # Engineering principles — how code should be written and structured
├── system-architecture.md  # Structural docs with freshness metadata
├── project-context.md      # Project goals and constraints
├── design-system.md        # UI/UX rules
├── repair-system.md        # Error knowledge base
├── planning/           # Task queue and project plan (with complexity tagging)
├── memory/             # Decisions and lessons (with supersedes links)
├── index/              # Repo map and dependency graph (auto-regenerable)
├── testing/            # Test plan and results
├── checkpoints/        # Session log (append-only) + in-progress (singular, overwritten)
└── summaries/          # Development history
```

## Getting Started

1. Copy the `ai-system-kit/` directory into your project:
   - `.ai-context.md` at project root
   - `.ai-system/` with all subdirectories

2. Run the bootstrap command:
   ```
   Execute command: bootstrap-project.md
   Directive: [describe your project — e.g., "Next.js + Node.js marketplace app"]
   ```

3. Start your first development cycle:
   ```
   Execute command: dev-cycle.md
   ```

## Context Tiering

The system uses four tiers to adapt to any context budget:

| Tier | Token Budget | Files | When |
|------|-------------|-------|------|
| 1 — Always Read | ~1.5k | .ai-context.md, context-tiering.md, quality-gate.md, escalation-rules.md | Every session |
| 2 — Task Scope | ~3k | task-queue.md, role file, system-architecture.md, repair-system.md | When task is known |
| 3 — On Demand | ~5k | project-context.md, design-system.md, memory/, index/ | When domain is touched |
| 4 — Reference | ~1.5k | architecture-history.md, test-plan.md, session-log.md | When explicitly needed |

## Quality Gate

Every command runs this checklist before declaring work complete:

1. **Requirement match** — actual stated requirement, not reinterpretation
2. **Generalization check** — works beyond the example case
3. **Scope discipline** — only files relevant to the task
4. **Architecture consistency** — matches documented structure
5. **No unstated assumptions** — assumptions are logged, not baked in
6. **Error-path completeness** — failure cases handled
7. **Self-verification** — how you verified, not "should work"
8. **No re-prompt debt** — resolve or explicitly flag follow-ups
9. **Pattern Adherence** — follows engineering principles (config-driven, no duplicate types, appropriate modularization)

## Key Commands

| Command | Purpose |
|---------|---------|
| `bootstrap-project.md` | Initialize .ai-system documentation |
| `execute-feature.md` | Full feature pipeline (plan → implement → QA → doc) |
| `dev-cycle.md` | Daily development loop |
| `plan-feature.md` | Architecture impact analysis (no code) |
| `refactor-codebase.md` | Structural improvement |
| `fix-build.md` | Self-healing error diagnosis and fix |
| `verify-work.md` | Standalone quality gate |
| `sync-context.md` | Mid-work lightweight doc sync |
| `resume-session.md` | Interruption recovery |
| `cloud-session.md` | Async/unattended session protocol |
| `update-ai-system.md` | Sprint-end deep sync |
| `audit-drift.md` | Deep consistency check |

## Integration

The `.ai-system` kit works with any AI tool. The only requirement is that your AI tool reads `.ai-context.md` at session start.

For example integration notes, see `integrations/examples/tool-integration.md` (non-normative, optional).

## License

This system is provided as a reference architecture for AI-assisted development. Use, modify, and distribute freely.
