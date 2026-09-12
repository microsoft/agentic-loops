---
name: preflight
description: The assistant runs this before the loop. Blocks non-assistants, unresolved placeholders, and stale required user skills.
---

Run before entering the loop. Stop on any failure.

## Gate 1 — Assistant only

Only `.github/agents/<Persona>.md` runs the loop. Anders, Dave, Bhaskar, and other delegated agents
refuse and return control to the assistant.

## Gate 2 — Required placeholders

From the repository root, scan Markdown under `AGENTS.md`, `.github/`, and `docs/` for the opening
placeholder sentinel. Exclude this file because it contains the search term.

List every match and stop. Keep the literal sentinel out of explanatory prose elsewhere.

## Gate 3 — Required user skills

Keep these skills user-scoped. Never copy them into the project.

| Skill | GitHub source |
|-------|---------------|
| `bro` | `https://github.com/microsoft/agentic-loops/tree/master/skills/bro` |
| `yagni` | `https://github.com/microsoft/agentic-loops/tree/master/skills/yagni` |

Use `gh skill list --agent github-copilot --scope user --json skillName,path,sourceURL,version` to find
installed copies. For each required skill, substitute its name for `<skill>` below:

1. If missing, without source metadata, or its `sourceURL` is not
   `https://github.com/microsoft/agentic-loops`, install it from the source above:
   `gh skill install microsoft/agentic-loops <skill> --agent github-copilot --scope user --force`.
   Do this before any update lookup so retired sources are never queried.
2. Otherwise, compare before writing:
   `gh skill update <skill> --dry-run --dir <user-skill-root>`.
   Use the parent of its listed `path` as `<user-skill-root>` to avoid other hosts and scopes.
   If changed, run the install command above.
3. After an install, re-read the user-scoped list and confirm the expected skill and source.

Any lookup, comparison, or install failure blocks. If a skill changed, stop and ask the human to
restart the session so Copilot loads it.
A skipped comparison, such as a pinned install, does not establish freshness; stop and ask the human.

## Project gates

_Add project-specific startup gates here, starting at Gate 4. State the check, failure message, and
whether it blocks._

## Pass

Proceed to mode selection: trunk means new-work mode; `{{WORK_BRANCH}}` means WIP mode.
Follow the installed workflow in `docs/meta-design.md`; WIP loads `{{WORK_RECORD}}`.
Do not offer a different workflow at startup or switch a checkout automatically.
