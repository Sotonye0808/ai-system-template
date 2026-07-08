# AI Dev Workflow — Personal Reference

> Your personal reference for the `ai-system` AI-assisted development workflow. Tool-agnostic — adapt to whichever AI tool you use.

---

## Quick Start

### Every Session

```
Read ai-context.md before responding.
```

### Common Commands

```
Execute command: dev-cycle.md                      → daily loop
Execute command: execute-feature.md                → full feature pipeline
Execute command: plan-feature.md                   → plan before coding
Execute command: fix-build.md                      → fix errors
Execute command: verify-work.md                    → QA gate
Execute command: sync-context.md                   → mid-work doc sync
Execute command: resume-session.md                 → after interruption
Execute command: cloud-session.md                  → async unattended run
Execute command: audit-drift.md                    → deep consistency check
```

With a directive:

```
Execute command: execute-feature.md
Directive: add JWT authentication with refresh tokens
```

---

## `ai-system` File Reference

| File                              | Purpose                                          | Updated By                           |
| --------------------------------- | ------------------------------------------------ | ------------------------------------ |
| `ai-context.md`                   | Project overview — first thing every agent reads | bootstrap-project                    |
| `protocols/entry-protocol.md`     | Session start procedure                          | bootstrap-project                    |
| `protocols/context-tiering.md`    | Progressive disclosure rules                     | bootstrap-project                    |
| `protocols/quality-gate.md`       | Anti-slop QA checklist                           | bootstrap-project                    |
| `protocols/escalation-rules.md`   | When to ask vs. proceed                          | bootstrap-project                    |
| `protocols/verification-rules.md` | How to verify each criterion                     | bootstrap-project                    |
| `agents/planner.md`               | Planning role                                    | bootstrap-project                    |
| `agents/architect.md`             | Architecture role                                | bootstrap-project                    |
| `agents/implementer.md`           | Implementation role                              | bootstrap-project                    |
| `agents/reviewer.md`              | Review role                                      | bootstrap-project                    |
| `agents/tester-qa.md`             | Testing role                                     | bootstrap-project                    |
| `agents/historian.md`             | Documentation role                               | bootstrap-project                    |
| `system-architecture.md`          | System structure                                 | Architect / generate-architecture    |
| `project-context.md`              | Goals, users, constraints                        | bootstrap-project                    |
| `design-system.md`                | UI/UX rules                                      | bootstrap-project                    |
| `repair-system.md`                | Known errors                                     | fix-build                            |
| `planning/task-queue.md`          | Sprint tasks with complexity tags                | dev-cycle / you                      |
| `planning/project-plan.md`        | High-level feature checklist                     | bootstrap-project                    |
| `checkpoints/session-log.md`      | Append-only work log                             | All agents                           |
| `checkpoints/in-progress.md`      | Singular in-progress marker                      | execute-feature / dev-cycle          |
| `memory/project-decisions.md`     | Decision log with supersedes links               | Architect / implementer              |
| `memory/lessons-learned.md`       | Process insights with supersedes links           | update-ai-system                     |
| `memory/architecture-history.md`  | Architecture evolution                           | Architect                            |
| `index/repo-map.md`               | Folder structure (auto-regenerable)              | bootstrap-project / update-ai-system |
| `index/dependency-graph.md`       | Module relationships (auto-regenerable)          | bootstrap-project / update-ai-system |
| `testing/test-plan.md`            | Test coverage definition                         | tester-qa                            |
| `testing/test-results.md`         | Latest test run output                           | tester-qa / fix-build                |
| `summaries/dev-history.md`        | Chronological sprint summaries                   | Agents at sprint end                 |

---

## Quality Gate Checklist (Quick Reference)

Before declaring work done, verify:

1. **Requirement match** — does it meet the actual directive?
2. **Generalization** — works beyond the example case?
3. **Scope** — only touched relevant files?
4. **Architecture** — consistent with system-architecture.md?
5. **Assumptions** — logged, not baked in?
6. **Error paths** — failure cases handled?
7. **Self-verification** — how did you verify (not "should work")?
8. **No re-prompt debt** — any obvious follow-ups resolved or flagged?
9. **Pattern Adherence** — follows engineering principles (config-driven, no duplicate types)?

---

## Multi-Agent Pattern

| Role        | Prompt Example                                                               |
| ----------- | ---------------------------------------------------------------------------- |
| Planner     | `Read planning/task-queue.md and determine the next development step`        |
| Architect   | `Update system-architecture.md to support the next task`                     |
| Implementer | `Implement the first task from the queue following architecture constraints` |
| Reviewer    | `Review the last changes for architecture consistency and quality`           |
| Tester      | `Write and run tests for the implemented feature`                            |
| Historian   | `Log decisions and update dev-history for this session`                      |

---

## Prompt Tips

- Always start sessions with: `Read ai-context.md before answering`
- Use directives to refine commands without rewriting them: `Directive: your instruction`
- For multi-step features, use `execute-feature.md` — it handles the full pipeline
- If an agent drifts off-task: `Check planning/task-queue.md and continue from where you left off`
- Run `sync-context.md` during long sessions to prevent context-rot
- If interrupted, run `resume-session.md` — not `dev-cycle.md`
- For async or background work, use `cloud-session.md` — it has stricter safety guards

---

## When Things Go Wrong

| Problem                   | Solution                                                          |
| ------------------------- | ----------------------------------------------------------------- |
| Agent ignoring docs       | `Read protocols/entry-protocol.md before responding`              |
| Overfit solutions         | Run `verify-work.md` focusing on generalization check             |
| Scope creep               | Check scope discipline in quality gate; revert out-of-scope files |
| Stale docs mid-sprint     | Run `sync-context.md`                                             |
| Session interrupted       | Run `resume-session.md`                                           |
| Architecture drift        | Run `audit-drift.md`                                              |
| Bootstrap generic content | Add a detailed directive                                          |
