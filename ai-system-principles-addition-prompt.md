# Prompt for opencode: Add Engineering Principles Standard + Close Minor Gaps

> Paste this into your opencode session, in the same repo (`ai-system-template`), after the v2 revamp already committed. This builds on that work — read the current `ai-system/` state first, don't start from assumptions.

---

You previously revamped `ai-system` into the vendor-neutral, protocol-driven v2 system (protocols/, agents/, commands/, etc.). This task has two parts: (A) add a new engineering-standards layer that the system actively enforces, and (B) close a few small consistency gaps left over from the v2 build.

## Part A — New file: `ai-system/standards/engineering-principles.md`

Create a new top-level subdirectory `ai-system/standards/` (sibling to `protocols/`, `agents/`, `commands/`) and add `engineering-principles.md` as the canonical doctrine for _how code should be written and structured_ in any project using this system — distinct from `quality-gate.md` (which governs verification of finished work) and `design-system.md` (which governs visual/UX specifics per project). This file is **process-and-pattern-agnostic of any single project** — it's the standing engineering philosophy, the way `quality-gate.md` is a standing QA philosophy.

Use the same metadata header convention as every other file (`last-updated-by`, `last-verified-against-code`, `staleness-policy: this file changes rarely — trust unless explicitly flagged`), and the same "Overview" + section-summary skimmability convention.

Content to include (write this in your own words/structure, matching the house style of the existing protocol files — short, concrete, example-bearing, not abstract essay-style):

### 1. Config-driven over hardcoded

Behavior, limits, feature flags, and business rules live in configuration (env vars, config files, database-backed settings, feature-flag services), not inline in logic. State the test: "if a non-engineer might reasonably want to change this value without a code deploy, it belongs in config." Hardcoded values are acceptable only as **fallback defaults** layered beneath config — never as the sole source of truth. Every config-driven value must have a documented, safe fallback so the system degrades gracefully (not crashes) if config is missing/malformed.

### 2. Metadata-driven structure

Where it's a good fit (admin-editable content types, dynamic forms, permission systems, navigation/menus), define structure as data/metadata rather than hardcoded markup or branching logic — so new instances of a "thing" (a new content type, a new field, a new role) can be added by changing data, not by writing new code paths. State the trade-off honestly: this adds indirection, so it should be used where variability is real and expected, not applied reflexively to everything.

### 3. Admin-editability with hardcoded fallbacks

Anything public-facing or operationally significant that a non-engineer would plausibly need to change (copy, global variables/settings, feature toggles, pricing, contact info, banner content) should be editable through an admin surface or config layer — backed by a hardcoded fallback value/constant in code so the system never breaks or shows blank/broken content if the admin-editable value is unset, deleted, or the data layer is unavailable. This is the same fallback discipline as #1, applied specifically to operator/admin-facing content.

### 4. Universal components & wrappers

Prefer a small set of well-designed, reusable, configurable base components/wrappers over many bespoke one-off implementations. New UI elements or service wrappers should default to extending or composing existing universal components before a new one is created — and creating a new one should be a deliberate, justified decision (logged), not the default path. Wrappers around third-party libraries/SDKs (auth providers, payment processors, UI kits) should isolate the third-party API behind a stable internal interface, so swapping the underlying provider doesn't ripple through the whole codebase.

### 5. Global definitions: styling, config, types/interfaces

Single source of truth for: design tokens (colors, spacing, typography — these should be _defined once_ and consumed everywhere, never re-declared per component), global configuration shape, and shared TypeScript types/interfaces (or language equivalent). No duplicate/parallel type definitions for the same concept in different files. State the rule: "if two modules need to agree on the shape of something, that shape is defined in exactly one place and imported, never copy-pasted or redefined."

### 6. Modularization & separation of concerns

Each module/class/function has one clear responsibility. Favor composition over deep inheritance chains. Apply standard OOP discipline where the language supports it (encapsulation, interface-first design, dependency injection over hard-coded instantiation) without forcing OOP patterns onto code that's naturally functional/data-oriented — match the paradigm to the problem, don't apply a hammer everywhere.

### 7. Containerization & environment parity

Where applicable, favor containerized, reproducible environments (Docker or equivalent) so "works on my machine" issues are structurally prevented, and config/env differences between local, staging, and production are explicit and versioned rather than tribal knowledge.

### 8. Lean, efficient, dynamic, maintainable code

- Lean: no speculative abstraction for requirements that don't exist yet (YAGNI) — but also no copy-pasted duplication that should be a shared function (DRY). Both extremes are smells.
- Efficient: be conscious of algorithmic complexity, unnecessary re-renders/re-computation, and N+1 query patterns — but don't micro-optimize at the cost of readability where performance isn't actually a constraint.
- Dynamic: code should accommodate reasonable variation (different environments, scales, locales, tenants if multi-tenant) without requiring a code change for each variation — this connects back to #1 and #2.
- Maintainable: a developer unfamiliar with this specific change should be able to understand it from the code + its documentation without needing to ask the original author.

### 9. Documentation: concise, clear, and exists

- Every non-trivial function/module gets a short doc comment: what it does, why (if not obvious), and any non-obvious constraints/gotchas — not a restatement of the function signature in prose.
- Prefer self-documenting names over comments explaining bad names.
- README/architecture docs stay in sync with code (this connects directly to `protocols/quality-gate.md` and `commands/sync-context.md` — documentation drift is itself a QA gate failure, not a separate concern).
- No long comment blocks explaining "how" when the code itself could be made clear enough to not need it; comments are for "why," not "what."

### 10. How this gets enforced

Close the file with a short section stating explicitly: this document is not aspirational — it is checked. List exactly where: the Architect role consults it when designing structure; the Implementer role consults it during coding; `protocols/quality-gate.md` references it under a "Pattern Adherence" criterion (see Part A.2 below); `verify-work.md` checks against it for anything beyond a trivial change.

## Part A.2 — Wire it into the existing system (don't leave it orphaned)

Make these specific, surgical edits to existing files so the new standard is actually load-bearing, not a document nobody reads:

1. **`protocols/quality-gate.md`** — add a 9th checklist item: **"Pattern Adherence"** — does the implementation follow `standards/engineering-principles.md` (config-driven, no hardcoded values that should be configurable, no duplicate type/style definitions, appropriate modularization)? This sits alongside the existing 8 criteria, with the same format (a short explanation + what to check). Update the "Mandatory Checklist" intro line if it references "8 criteria" elsewhere (check `verify-work.md` and `README.md` for that exact phrasing and update both).

2. **`protocols/verification-rules.md`** — add a corresponding "How to Verify: Pattern Adherence" subsection with concrete checks (e.g., "grep for repeated magic values across files," "check whether new types duplicate an existing shape," "check whether new UI elements could have reused an existing universal component").

3. **`protocols/context-tiering.md`** — add `standards/engineering-principles.md` to **Tier 2** (read when you have a task), since it should be read before most implementation work, not buried in Tier 3/4. Note its token budget.

4. **`protocols/entry-protocol.md`** — add a row to the "Quick Navigation" table: "Engineering standards / how to write code" → `standards/engineering-principles.md`.

5. **`agents/architect.md`** — add it to the Inputs list; note in the role's Quality Bar that proposed architecture must align with it.

6. **`agents/implementer.md`** — add it to the Inputs list; add at least one line to "Explicitly NOT Allowed" reflecting it directly, e.g. "Hardcoding a value that should be config-driven without a documented fallback" and "Duplicating an existing type/interface instead of importing it."

7. **`design-system.md`** — add a short note near the top (in the Overview or a new short section) explicitly stating that the color/typography/spacing tables in this file ARE the single source of truth referenced by principle #5, and that components must consume these tokens rather than redeclaring values.

8. **`system-architecture.md`** — add a short "Engineering Standards" pointer near "Configuration Points" noting that all listed config points should follow the fallback discipline from `standards/engineering-principles.md` §1/§3.

9. **`commands/bootstrap-project.md`** — add `standards/engineering-principles.md` to the list of files it seeds/verifies metadata on (it's a static doctrine file like the protocols, so bootstrap should just stamp its metadata header, not generate project-specific content into it).

10. **`README.md`** — add `standards/` to the Directory Contract tree, and add one line under "Philosophy" reflecting this addition (e.g., "**Pattern discipline.** Config-driven, fallback-safe, single-source-of-truth engineering standards are enforced through the quality gate, not left as unstated convention.").

11. **`MIGRATION.md`** — add an entry under "Files Added" for `standards/engineering-principles.md`, and a short note in "Key Behaviour Changes to Note" that the quality gate now has 9 criteria, not 8.

## Part B — Close the small consistency gaps from the v2 build

1. **`commands/fix-build.md`** — add a step instructing it to write `checkpoints/in-progress.md` before starting diagnosis (mirroring how `dev-cycle.md` and `execute-feature.md` do it), and clear it on completion. A fix-build session is multi-step and can be interrupted just like any other.

2. **`commands/audit-drift.md`** — same: write a minimal `in-progress.md` entry before starting the audit (it can be a long-running check across many files), clear it when the report is produced.

3. **Trigger cadence for staleness.** Add a short note to `protocols/context-tiering.md` or `protocols/entry-protocol.md` (your call which fits better) stating: if any file's `last-verified-against-code` is missing or clearly stale relative to recent activity, the agent should not block on it, but should note it and prefer `sync-context.md` proactively rather than waiting for a scheduled audit. This closes the gap where staleness metadata exists but nothing prompts the agent to act on it outside of explicit `audit-drift` runs.

## Working method

1. Read the current `ai-system/` state in full before editing — confirm exact current wording in the files you're touching (especially the "8 criteria" phrasing you need to find and update to 9) rather than assuming.
2. Make Part B's small fixes first (low-risk, mechanical).
3. Then write `standards/engineering-principles.md` in full.
4. Then make every Part A.2 wiring edit, and grep the repo afterward for any other place that says "8 criteria"/"8-criterion" that you may have missed.
5. Run your own quality gate (the one you just edited) against this change before declaring it done — including the new "Pattern Adherence" criterion, applied reflexively to this very change: are there hardcoded counts ("8 criteria") anywhere else that should have been updated and weren't?
