---
name: build-test-full
description: "Runs the full build/test gate: this stack's verification steps, the complete test suite (unit + integration + acceptance), plus every optional quality gate that is set. Bhaskar's full done-done gate."
---

**This file is the gate recipe of this repository.** Change it for this stack.

The recipe resolves names from the Commands table in `.github/copilot-instructions.md`. This file
never hardcodes shell commands.

This recipe is **authoritative for gate membership and order**. The `Gate` column of the Commands
table is only a hint. Change the order for this stack as necessary. Put a pre-build step (`restore`,
`type-check`) first. Put a type-aware linter that needs compiled output *after* `build`.

## The full gate

Run these commands in this order. Resolve each name from the Commands table. A step with the Value
`none` is not part of the gate for this stack. Delete its line. Do not keep a step that never runs.

1. `format:check`: check the formatting only. This variant does **not** write. _(if set)_
2. `lint` _(if set)_
3. `build`
4. `test:full`: unit + integration + acceptance
5. `dry-check`: duplication/DRY checker _(if set)_
6. `mutation-test`: mutation tester _(if set)_
7. `crap-check`: CRAP metric, complexity × coverage _(if set)_

Then run the additional commands that this stack added to the Commands table.

**Run rules.** `build` and `test:full` are **required**. If one of them is `none` or empty, that is
a **misconfiguration**: **stop and report it**. Never skip it silently. All other rows are optional.
If the Value of a row is `none`, that step is not part of this gate. If a gate is `none` because this
stack enforces it in a different way (for example, a build configuration that already fails on
analyzer/style diagnostics), write that here. Then the absence is a recorded decision, not an error.

**One implementation per step.** If a step must operate identically in CI and locally, it has
exactly one implementation: a script that both call. Never put one version in the CI YAML and a
different version here.

**Each optional gate that is set runs on EVERY change.** Stronger and more varied constraints give
tighter supervision of the work of the agents. Thus, each optional row that you fill in makes the
gate stricter.

**Test categories** follow
[`docs/meta-design.md#writing-tests`](../../docs/meta-design.md#writing-tests). The marking and
selection of each category (test traits, filters, separate harnesses) is specific to the stack.
Record the mechanism of this stack here, so that the classification is enforced and is not only a
goal.

**Zero tolerance.** Each warning or error fails the gate. Bhaskar does not pass a change until this
gate runs fully with no warnings and no errors.
