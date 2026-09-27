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
| `bro` | `https://github.com/luchasarie/bro-skill/blob/main/SKILL.md` |
| `simple-docs` | `https://github.com/parthopdas/skills/tree/main/skills/simple-docs` |

Use `gh skill list --agent github-copilot --scope user --json skillName,path,sourceURL,version` to find
installed copies. Compare before writing:

- **`simple-docs`:** use `gh skill update simple-docs --dry-run`. If missing or changed, run
  `gh skill install parthopdas/skills simple-docs --agent github-copilot --scope user --force`.
- **`bro`:** its root `SKILL.md` is not discoverable by `gh skill`. Fetch the raw GitHub file and
  compare it with the installed `SKILL.md`. If missing or changed, remove the installed copy when
  present, then run
  `copilot plugins install --skill --scope user https://raw.githubusercontent.com/luchasarie/bro-skill/main/SKILL.md`.

Any lookup, comparison, or install failure blocks. If a skill changed, stop and ask the human to
restart the session so Copilot loads it.

## Project gates

<!-- OPTIONAL:DOTFILES:BEGIN -->
## Gate 4 — Local dotfiles

Run the user dotfiles installer when it is already present. This block is personal to the framework
checkout. The agentify installer removes the whole block.

```text
+--------------------------------+ missing +------+
| D:\src\gh\dotfiles\install.ps1 | ------> | Pass |
+--------------------------------+         +------+
        | found
        v
   run install.ps1
        | 0                 | non-zero
        v                   v
     Pass                 Stop
```

Boxes are checks. Arrows are the next check.

1. If `D:\src\gh\dotfiles\install.ps1` is missing, continue.
2. Run `powershell -NoProfile -ExecutionPolicy Bypass -File D:\src\gh\dotfiles\install.ps1`.
3. That script Applies only. It must not Capture or Publish.
4. A missing checkout, offline Scoop, and a failed Scoop install do not stop the loop.
5. Do not run `sync.ps1`. Capture and Publish are manual.
6. A non-zero exit stops the loop. Show the script error.
<!-- OPTIONAL:DOTFILES:END -->

## Pass

Proceed to mode selection: trunk means new-work mode; `{{WORK_BRANCH}}` means WIP mode.
Follow the installed workflow in `docs/meta-design.md`; WIP loads `{{WORK_RECORD}}`.
Do not offer a different workflow at startup or switch a checkout automatically.
