---
name: build-test-full
description: Runs the full build/test gate — format check, lint, build, the complete test suite (unit + integration), plus every optional quality gate that is set. Bhaskar's full done-done gate.
---

Framework-owned **recipe** — no placeholders. It runs the commands named in the **Project profile →
Commands** table (`.github/copilot-instructions.md`); that table is the single source of truth for the
actual shell commands, so this file never hardcodes them.

This recipe is **authoritative for gate membership and order**; the Commands table's `Gate` column is a
hint. Reorder for your stack if needed (e.g. type-aware linters that require compiled output should run
after `build`).

## The full gate (Bhaskar)

Run these commands in order, resolving each name from the Commands table:

1. `format:check` — check formatting only; this variant does **not** write
2. `lint`
3. `build`
4. `test:full` — unit + integration
5. `dry-check` — duplication/DRY checker _(if set)_
6. `mutation-test` — mutation tester _(if set)_
7. `crap-check` — CRAP metric, complexity × coverage _(if set)_

…plus any additional commands the consumer added to the Commands table.

**Run rules.** The skip-`none` rule applies **only to the optional rows** (`dry-check`, `mutation-test`,
`crap-check`): when their Value is `none` they don't run. A **required** command (`format:check`,
`lint`, `build`, `test:full`) whose Value is `none` or empty is a **misconfiguration**: **stop and
report it** — never silently skip.

**Every optional gate that is set runs on EVERY change.** The stronger and more varied these
constraints, the more tightly the agents' work can be supervised — so a consumer who fills in more
optional rows gets a stricter gate.

**Zero tolerance.** Any warning or error fails the gate. Bhaskar does not pass a change until this runs
completely clean.
