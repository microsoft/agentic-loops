---
name: build-test-full
description: Runs the full build/test gate — this stack's verification steps, the complete test suite (unit + integration + acceptance), plus every optional quality gate that is set. Bhaskar's full done-done gate.
---

**This repository's gate recipe.** Tailor it to this stack.

It resolves names from the Commands table in `.github/copilot-instructions.md`; this file never
hardcodes shell commands.

This recipe is **authoritative for gate membership and order**; the Commands table's `Gate` column is a
hint. Reorder for this stack as needed — a pre-build step (`restore`, `type-check`) goes first, and a
type-aware linter that needs compiled output goes *after* `build`.

## The full gate

Run these commands in order, resolving each name from the Commands table. Steps whose Value is `none`
are not part of this stack's gate — delete the line rather than leaving a step that never runs:

1. `format:check` — check formatting only; this variant does **not** write _(if set)_
2. `lint` _(if set)_
3. `build`
4. `test:full` — unit + integration + acceptance
5. `dry-check` — duplication/DRY checker _(if set)_
6. `mutation-test` — mutation tester _(if set)_
7. `crap-check` — CRAP metric, complexity × coverage _(if set)_

…plus any additional commands this stack added to the Commands table.

**Run rules.** `build` and `test:full` are **required** — either one `none` or empty is a
**misconfiguration**: **stop and report it** (never silently skip). Every other row is optional: when
its Value is `none` the step simply isn't part of this gate. If a gate is `none` because this stack
enforces it another way — e.g. a build configuration that already fails on analyzer/style diagnostics —
say so here, so the absence reads as a decision rather than an oversight.

**One implementation per step.** A step that must behave identically in CI and locally has exactly one
implementation — a script both call. Never one version in the CI YAML and another here.

**Every optional gate that is set runs on EVERY change.** The stronger and more varied these
constraints, the more tightly the agents' work can be supervised — so filling in more optional rows
buys a stricter gate.

**Test categories** follow
[`docs/meta-design.md#writing-tests`](../../docs/meta-design.md#writing-tests). How each category is
marked and selected (test traits, filters, separate harnesses) is stack-specific — record this stack's
mechanism here so the classification is enforceable rather than aspirational.

**Zero tolerance.** Any warning or error fails the gate. Bhaskar does not pass a change until this runs
completely clean.
