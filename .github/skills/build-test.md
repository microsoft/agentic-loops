---
name: build-test
description: Runs the fast build/test gate — the quick-feedback steps this stack defines, ending in build + unit tests (excludes integration and acceptance tests). Dave's fast done-done gate.
---

**This repository's gate recipe.** It is seeded by the `agentify` skill from the install interview and
is **consumer-owned** thereafter — a framework update never overwrites it. Tailor it freely to this
stack; it carries no placeholders.

It runs the commands named in the **Project profile → Commands** table
(`.github/copilot-instructions.md`); that table is the single source of truth for the actual shell
commands, so this file never hardcodes them.

This recipe is **authoritative for gate membership and order**; the Commands table's `Gate` column is a
hint. Reorder for this stack as needed — a pre-build step (`restore`, `type-check`) goes first, and a
type-aware linter that needs compiled output goes *after* `build`.

## The fast gate

Run these commands in order, resolving each name from the Commands table. Steps whose Value is `none`
are not part of this stack's gate — delete the line rather than leaving a step that never runs:

1. `format:fix` — auto-format; this variant **writes** changes _(if set)_
2. `lint` _(if set)_
3. `build`
4. `test:quick` — unit tests only

…then any additional commands this stack added to the Commands table.

**Run rules.** `build` and `test:quick` are **required** — either one `none` or empty is a
**misconfiguration**: **stop and report it** (never silently skip). Every other row is optional: when
its Value is `none` the step simply isn't part of this gate. If a gate is `none` because this stack
enforces it another way — e.g. a build configuration that already fails on analyzer/style diagnostics —
say so here, so the absence reads as a decision rather than an oversight.

**One implementation per step.** A step that must behave identically in CI and locally has exactly one
implementation — a script both call. Never one version in the CI YAML and another here.

**Unit-only.** Test categories follow
[`docs/meta-design.md#writing-tests`](../../docs/meta-design.md#writing-tests). The fast gate
deliberately does **not** run integration or acceptance tests, or the optional quality gates (DRY,
mutation, CRAP) — those belong to Bhaskar's full gate (`.github/skills/build-test-full.md`).

**Zero tolerance.** Any warning or error fails the gate. Dave is not done-done until this passes clean.
