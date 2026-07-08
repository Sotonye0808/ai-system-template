# Prompt for opencode: Revamp `ai-system` v2

> Paste everything below into your opencode session. It's written to be self-contained: it tells the agent what currently exists, what's wrong with it, and exactly what to build instead.

---

You are revamping an existing AI-assisted development framework called `ai-system` (currently shipped as a starter kit at `ai-system-kit/ai-system/` in the repo `Sotonye0808/ai-system-template`). You have read access to the current repo state — use it as your source of truth for what already exists; do not invent structure that isn't there or assume content you haven't actually read.

## 1. Why this revamp is happening

The current system works but has four structural problems:

1. **Tool/model lock-in.** The README, `agent-bootstrap.md`, `continue-config.yaml`, the "Agent Roles" table in `general-instructions.md`, and the setup guides all hardcode specific products: Continue, Cline, Supermaven, Sourcegraph Amp, Ollama, Together AI, Groq, OpenRouter, specific model IDs (`qwen/qwen3-coder:free`, `deepseek-coder:6.7b`, etc.). The system should work identically whether the operator is using Claude Code, opencode, Cursor, Windsurf, Continue, Cline, a raw API loop, or a brand-new tool that doesn't exist yet. **No file in the new system should name a specific AI product, vendor, or model.** Roles (Planner, Architect, Coder, Reviewer, Tester, Historian) must be defined as _functions_, not bound to specific tools.

2. **No context-budget resilience.** The current files assume an agent will read 5–7 files before doing anything, with no fallback for a small-context or cheap/fast model. There's no tiering — a 200k-context frontier model and a small fast model are expected to consume the same volume of upfront reading. The new system needs a **context-tiering protocol**: a minimal "always read" core (small, <1-2k tokens) plus progressive disclosure (deeper files loaded only if the task needs them), so the system degrades gracefully instead of failing or causing the agent to skip reading entirely.

3. **Thin automation, no closed loop.** Commands today are essentially static prompts that say "read X, do Y, update Z" — there's no quality gate, no drift detection, no rollback path, no explicit handling of interruption/resumption, no distinction between local and cloud/async sessions, and "update-ai-system" is a manual afterthought rather than something woven into every workflow. There's also no first-class **execute-feature** pipeline that runs planning → implementation → review → test → document as one governed pipeline (today `plan-feature.md` and `dev-cycle.md` are separate and only loosely connected).

4. **No anti-slop / anti-overfitting enforcement.** Nothing in the current system actively pushes back on overconfident output, premature implementation, scope-padding, hallucinated file references, or solutions overfit to one example/test case rather than the actual requirement. Quality is only checked reactively (via `self-heal.md`, which is test-failure-only) — there is no pre-emptive verification gate that runs _before_ code is considered "done," and no explicit mechanism asking "does this generalize, or did I just satisfy the visible case."

## 2. What must NOT change

- The directory must stay named `ai-system/` at the project root, with a top-level `ai-context.md` (or equivalent) as the universal entry point — that convention is intentional and other tooling/muscle memory depends on it.
- Keep the core idea of: agents-as-protocol-files, planning artifacts, checkpoints/session-log, memory (decisions/lessons/architecture history), an index (repo-map/dependency-graph), a repair-system, and a testing layer. These categories are good — they're being deepened and reorganized, not thrown out.
- Keep file-based state (Markdown + structured frontmatter where useful) as the persistence mechanism — no external database assumption, since this needs to work in any repo with any toolchain.
- Keep the "Directive: ..." pattern for parameterizing commands — it's simple and works across tools.

## 3. Required structural changes

### 3.1 Strip all vendor/tool/model references

Rewrite every file so it never says "Continue," "Cline," "Supermaven," "Sourcegraph Amp," "Ollama," "Together AI," "Groq," "OpenRouter," or any model name/ID. Replace tool-specific instructions ("paste into Continue chat," "hand off to Cline") with neutral language: "the active AI agent/session," "the coding agent." Remove `continue-config.yaml` and `start-ai-dev.bat` from the kit entirely (or relocate any genuinely tool-agnostic startup logic — e.g., a generic env-var-validation script — to an optional, clearly-labeled `integrations/` or `examples/` folder that is NOT part of the core `ai-system` contract). The README should describe the _system_, not a specific VS Code stack; if you want to keep an "example integration" section, isolate it clearly as optional and non-normative.

### 3.2 Rework `agents/` into role + protocol layers

Split into two clear concerns:

- **Protocol files** (how any agent should behave, regardless of role): entry protocol, context-tiering rules, anti-slop/verification rules, escalation rules (when to ask a clarifying question vs. proceed).
- **Role files** (function-based, not tool-based): Planner, Architect, Implementer, Reviewer, Tester/QA, Historian/Archivist, Repair Specialist. Each role file defines: inputs it must read, outputs it must produce, what it's explicitly NOT allowed to do (e.g., Implementer must not silently change architecture; Reviewer must not implement fixes itself, only flag and hand off), and its quality bar.

Keep `system-architecture.md`, `project-context.md`, `design-system.md`, `repair-system.md` — but tighten them with explicit "freshness contract" headers (see 3.5) and trim boilerplate so a small-context model can skim them fast.

### 3.3 Expand `commands/` substantially

Keep and refine the existing six (`bootstrap-project`, `dev-cycle`, `plan-feature`, `refactor-codebase`, `fix-build`/self-heal merged sensibly, `update-ai-system`), but add:

- **`execute-feature.md`** — the missing first-class pipeline: takes a feature directive, runs an internal planning pass (reusing plan-feature logic), gets explicit go/no-go confirmation criteria (self-checked against architecture + scope), implements, runs the QA gate (3.6), updates docs, and only then marks complete. This is the "do the whole thing properly, end to end, without needing a re-prompt" command.
- **`resume-session.md`** — for resuming after an interruption (crash, context reset, switching machines/agents). Must reconstruct full working state from `checkpoints/session-log.md` + `planning/task-queue.md` + any `checkpoints/in-progress/` marker (see 3.4) without re-reading the whole repo, and must explicitly verify whether the last logged state still matches the actual repo (drift check) before continuing.
- **`cloud-session.md`** (or `async-session.md`) — protocol for sessions that run unattended/asynchronously (e.g., a remote/background agent run) where there's no human in the loop to interrupt bad direction in real time. Must require tighter pre-authorized scope boundaries, mandatory checkpoint writes at a defined cadence (not just at the end), a hard stop-and-flag condition list (things the agent must never do unattended — destructive ops, dependency changes, schema migrations, etc.), and a clear human-handoff summary format for when the session ends.
- **`sync-context.md`** — a lighter, more frequent sibling to `update-ai-system.md`: meant to be invoked _during_ work (not just at sprint end) whenever the agent notices the docs and code have diverged, so context-rot doesn't accumulate across a long session. This is the "looping update" mechanism you asked for — commands like `execute-feature` and `dev-cycle` should explicitly invoke `sync-context` at internal checkpoints, not just at the very end.
- **`verify-work.md`** — standalone QA gate command (see 3.6), invokable on its own or as a required internal step of `execute-feature`/`dev-cycle`.
- **`audit-drift.md`** — periodic deep check comparing `ai-system/` claims against actual repo state (architecture doc vs. real code structure, task-queue claims vs. git history, repair-system entries vs. whether the bug actually recurred) and produces a discrepancy report.

Every command file should declare, at the top, in a small consistent header: required inputs, optional Directive examples, what it must NOT do, and what it guarantees on completion (its "contract").

### 3.4 Add interruption-safe state tracking

Introduce `ai-system/checkpoints/in-progress.md` (singular, always-current, overwritten not appended) that any command writes to _before_ starting risky multi-step work and clears on clean completion — distinct from `session-log.md`, which is the historical append-only record. `resume-session.md` should check `in-progress.md` first thing. This solves "agent got cut off mid-task and the next session has no idea what was half-done."

### 3.5 Add freshness/ownership metadata to every doc

Each `ai-system` file should have a short frontmatter or header block: `last-updated-by` (which command/role), `last-verified-against-code` (date or commit-ish marker), and `staleness-policy` (e.g., "re-verify before trusting if >N sessions old" or "trust only if no architecture-affecting commits since"). This gives agents a cheap way to decide whether to trust a doc at face value or re-derive it — critical for the "no deviation/no overfitting" goal, since stale docs are a major cause of agents confidently doing the wrong thing.

### 3.6 Add an explicit Quality Assurance / anti-slop gate

This is the most important addition. Create `ai-system/agents/quality-gate.md` (or fold into a protocol file) defining a mandatory checklist that `execute-feature`, `dev-cycle`, and `verify-work` all run before declaring work done:

- **Requirement match**: does the implementation address the actual stated requirement, not a convenient reinterpretation of it?
- **Generalization check**: would this solution work for inputs/cases beyond the one example discussed, or is it overfit to the literal sample (hardcoded values, magic-matching a test string, special-casing instead of solving the general case)?
- **Scope discipline**: did the agent touch only files relevant to the task (cross-check against `dependency-graph.md`)? Flag and justify any out-of-scope file touched.
- **Architecture consistency**: does the change match `system-architecture.md` and `design-system.md`, or does it introduce silent architecture drift that should have been flagged first?
- **No unstated assumptions**: any assumption made during implementation must be explicitly logged (to `memory/project-decisions.md` if significant, or inline in the session-log entry) rather than silently baked in.
- **Error-path completeness**: are failure/edge cases handled, not just the happy path?
- **Self-verification before handoff**: the agent must state _how_ it verified the work (tests run, manual trace, reasoning) — "should work" is not an acceptable closing statement; if verification wasn't possible, that must be stated explicitly as a residual risk.
- **No re-prompt debt**: before ending a turn, the agent should ask itself "is there an obvious follow-up question or fix the user/operator will have to come back and ask for?" and if yes, resolve it now or explicitly flag it rather than leaving it implicit.

This gate should also define **rollback guidance**: what to do if the QA gate fails after implementation (revert vs. patch-forward criteria).

### 3.7 MCP-aware operation

Since real sessions increasingly have MCP tools available (filesystem, git, search, issue trackers, CI status, etc.), the system should explicitly account for this without depending on it:

- Add a short section (in the entry protocol file) on **tool-discovery-first behavior**: before assuming something must be done by hand (e.g., grepping manually, asking the user for file contents), the agent should check what tools/MCP servers are actually available in the current session and prefer using them for ground-truth (e.g., live git status/log instead of trusting a stale `dependency-graph.md`, live test runner output instead of asking the user to paste errors).
- Make clear that `ai-system` docs are a _cache/summary layer_ over the real repo, not a replacement for it — MCP/tool access should always be trusted over stale docs when they conflict, and that conflict should trigger `sync-context.md`.
- Don't hardcode specific MCP server names — describe capabilities needed (repo search, git introspection, test execution, issue tracker if present) generically.

### 3.8 Tighten `planning/`, `memory/`, `index/`, `testing/`

- `task-queue.md`: add explicit task sizing/complexity tagging so an agent can self-select whether a task needs the full `execute-feature` pipeline or a lighter touch.
- `memory/project-decisions.md` and `lessons-learned.md`: add a short "supersedes/superseded-by" link convention so contradictory historical decisions don't both look equally valid.
- `index/repo-map.md` and `dependency-graph.md`: explicitly mark these as auto-regenerable from tool/MCP introspection where possible, with manual content only where it can't be derived.
- `testing/`: connect explicitly to the QA gate — `test-plan.md` should be referenced inside `verify-work.md`, not just `self-heal`.

### 3.9 Rewrite `agent-bootstrap.md` / `bootstrap-project.md`

Merge these two (they currently overlap almost entirely) into one clear bootstrap command, vendor-neutral, that also seeds the new files (`in-progress.md`, frontmatter headers, quality-gate.md, etc.) — not just the original set.

### 3.10 README and any setup/reference docs

Rewrite to describe the system itself (philosophy, directory contract, how commands compose into pipelines, the context-tiering model, the QA gate) rather than a specific editor/extension stack. If you keep any example integration notes, clearly separate them under a non-normative "Example: wiring this into a specific tool" section, and keep that generic/swappable (described as "wire your AI tool to read `ai-context.md` at session start" rather than naming a product).

## 4. Deliverable format

Produce the revamped system as a real, navigable directory tree replacing the current `ai-system-kit/ai-system/` (plus updated `ai-context.md`/README at the appropriate level), not a single mega-document. Use clear, skimmable Markdown, short overview blurbs at the top of every file (this convention is good — keep it), and consistent terminology across all files (pick one term per concept — e.g., "Directive" — and use it everywhere, don't introduce synonyms).

When done, produce a short `MIGRATION.md` explaining, for someone with an existing `ai-system` deployed from the old kit, what changed and what they need to do to upgrade an existing project (not just fresh installs).

## 5. Working method for this session

1. First, fully read the current `ai-system` kit (all files) and the README/setup guides so your revamp is grounded in what's actually there — don't paraphrase from memory.
2. Propose the new directory tree (file list only) and a one-line purpose for each file, before writing content, so the structure can be sanity-checked.
3. Then build out the files, starting with the protocol/entry-point layer (since everything else depends on it being right), then commands, then the rest.
4. As you build, hold yourself to the same QA gate you're designing — verify your own output isn't vendor-coupled, isn't overfit to this one repo's specifics in a way that breaks portability, and doesn't leave obvious gaps that would force a re-prompt.
