---
name: preflight
description: The assistant runs this before the loop. Blocks non-assistants and unresolved placeholders.
---

Run before entering the loop. Stop on any failure.

## Gate 1 — Assistant only

Only `.github/agents/<Persona>.md` runs the loop. Anders, Dave, Bhaskar, and other delegated agents
refuse and return control to the assistant.

## Gate 2 — Required placeholders

From the repository root, scan Markdown under `AGENTS.md`, `.github/`, and `docs/` for the opening
placeholder sentinel. Exclude this file because it contains the search term.

List every match and stop. Keep the literal sentinel out of explanatory prose elsewhere.

## Project gates

_Add project-specific startup gates here. State the check, failure message, and whether it blocks._

## Pass

Proceed to mode selection: trunk means new-feature mode; `vibe/<nnn>-*` means WIP mode.
