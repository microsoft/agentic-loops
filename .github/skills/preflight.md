---
name: preflight
description: The assistant runs this before the loop. Blocks non-assistants, unresolved placeholders, and stale required user skills.
---

Run this skill before you start the loop. If a gate fails, stop.

## Gate 1: Assistant only

Only `.github/agents/<Persona>.md` runs the loop. Anders, Dave, Bhaskar, and other delegated agents
refuse and give control back to the assistant.

## Gate 2: Required placeholders

From the repository root, scan the Markdown files in `AGENTS.md`, `.github/`, and `docs/` for the
opening placeholder sentinel. Do not scan this file, because it contains the search term.

List each match and stop. Do not write the literal sentinel in explanatory text in other files.

## Gate 3: Required user skills

Keep these skills at user scope. Never copy them into the project.

| Skill | Files |
|-------|-------|
| `bro` | `SKILL.md`, `LICENSE` |
| `yagni` | `SKILL.md`, `LICENSE.agentic-loops` |

- Source: `https://raw.githubusercontent.com/microsoft/agentic-loops/master/skills/<skill>/<file>`
- Target: `~/.copilot/skills/<skill>/<file>`

Get each file with a plain HTTPS GET, for example `curl -fsSL` or `Invoke-WebRequest`. Do not use
`gh`. Do not use a token.

For each file in the table:

1. Download the source to a temporary file.
2. Compare the temporary file with the target, byte for byte.
3. If the target is missing or different, replace the target with the temporary file.
4. Delete the temporary file.

If a download or a write fails, the gate blocks. Do not use an old copy. If a file changed, stop and
ask the human to restart the session, so that Copilot loads the new skill.

## Project gates

_Add project-specific startup gates here, starting at Gate 4. For each gate, state the check, the
failure message, and if it blocks._

## Pass

Go to mode selection: trunk is new-work mode; `{{WORK_BRANCH}}` is WIP mode. Follow the installed
workflow in `docs/meta-design.md`. In WIP mode, load `{{WORK_RECORD}}`. At startup, do not offer a
different workflow. Do not switch a checkout automatically.
