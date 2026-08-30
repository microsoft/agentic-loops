---
name: preflight
description: The assistant runs this before the loop. Blocks non-assistants and unresolved placeholders.
---

Run before entering the loop. Stop on any failure.

## Gate 1 — Assistant only

Only `.github/agents/<Persona>.md` runs the loop. Anders, Dave, Bhaskar, and other delegated agents
refuse and return control to the assistant.

## Gate 2 — Required placeholders

From the repository root, scan required files for the opening placeholder sentinel:

    rg -F -n "<<FILL_ME:" .github/copilot-instructions.md docs/design.md .github/skills/build-test.md .github/skills/build-test-full.md

List every match and stop. Keep the literal sentinel out of explanatory prose in those files.

## Project gates

_Add project-specific startup gates here. State the check, failure message, and whether it blocks._

## Pass

Proceed to mode selection: trunk means new-feature mode; `vibe/<nnn>-*` means WIP mode.
