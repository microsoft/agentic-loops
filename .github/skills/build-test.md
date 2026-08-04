---
name: build-test
description: Runs the fast build/test gate — auto-format, lint, unit tests, and build (excludes integration tests). Dave's fast done-done gate.
---

Framework-owned **recipe** — no placeholders. It runs the commands named in the **Project profile →
Commands** table (`.github/copilot-instructions.md`); that table is the single source of truth for the
actual shell commands, so this file never hardcodes them.

This recipe is **authoritative for gate membership and order**; the Commands table's `Gate` column is a
hint. Reorder for your stack if needed (e.g. type-aware linters that require compiled output should run
after `build`).

## The fast gate (Dave)

Run these commands in order, resolving each name from the Commands table:

1. `format:fix` — auto-format; this variant **writes** changes
2. `lint`
3. `build`
4. `test:quick` — unit tests only

…then any additional commands the consumer added to the Commands table.

**Run rules.** Every core row above is **required** — a required command whose Value is `none` or empty
is a **misconfiguration**: **stop and report it** (never silently skip). The skip-`none` rule applies
only to the **optional** rows (`dry-check`, `mutation-test`, `crap-check`), which run in the full gate —
the fast gate has none.

**Unit-only.** The fast gate deliberately does **not** run integration tests or the optional
gates (DRY, mutation, CRAP) — those belong to Bhaskar's full gate
(`.github/skills/build-test-full.md`).

**Zero tolerance.** Any warning or error fails the gate. Dave is not done-done until this passes clean.
