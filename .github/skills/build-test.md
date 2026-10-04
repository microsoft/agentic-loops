---
name: build-test
description: "Runs the fast build/test gate: the quick-feedback steps this stack defines, ending in build + unit tests (excludes integration and acceptance tests). Dave's fast done-done gate."
---

**This file is the gate recipe of this repository.** Change it for this stack.

The recipe resolves names from the Commands table in `.github/copilot-instructions.md`. This file
never hardcodes shell commands.

This recipe is **authoritative for gate membership and order**. The `Gate` column of the Commands
table is only a hint. Change the order for this stack as necessary. Put a pre-build step (`restore`,
`type-check`) first. Put a type-aware linter that needs compiled output *after* `build`.

## The fast gate

Run these commands in this order. Resolve each name from the Commands table. A step with the Value
`none` is not part of the gate for this stack. Delete its line. Do not keep a step that never runs.

1. `format:fix`: auto-format. This variant **writes** changes. _(if set)_
2. `lint` _(if set)_
3. `build`
4. `test:quick`: unit tests only

Then run the additional commands that this stack added to the Commands table.

**Run rules.** `build` and `test:quick` are **required**. If one of them is `none` or empty, that is
a **misconfiguration**: **stop and report it**. Never skip it silently. All other rows are optional.
If the Value of a row is `none`, that step is not part of this gate. If a gate is `none` because this
stack enforces it in a different way (for example, a build configuration that already fails on
analyzer/style diagnostics), write that here. Then the absence is a recorded decision, not an error.

**One implementation per step.** If a step must operate identically in CI and locally, it has
exactly one implementation: a script that both call. Never put one version in the CI YAML and a
different version here.

**Unit-only.** Test categories follow
[`docs/meta-design.md#writing-tests`](../../docs/meta-design.md#writing-tests). The fast gate
intentionally does **not** run integration tests, acceptance tests, or the optional quality gates
(DRY, mutation, CRAP). Those gates are in the full gate of Bhaskar
(`.github/skills/build-test-full.md`).

**Zero tolerance.** Each warning or error fails the gate. Dave is not done-done until this gate
passes with no warnings and no errors.
