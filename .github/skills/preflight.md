---
name: preflight
description: JARVIS runs this at session/loop start. Enforces two gates before the agentic loop may run — (1) only JARVIS orchestrates; (2) no required FILL_ME placeholders remain. Halts and reports otherwise.
---

Run by **JARVIS** at the start of every session and before entering the loop. If either gate fails,
**do not start the loop** — report and stop.

## Gate 1 — Orchestrator identity (the loop is JARVIS-only)

If the invoking agent is **not JARVIS**, refuse and hand back:

> "Preflight: the agentic loop is JARVIS-only. Handing back — please invoke JARVIS."

Any other agent (Anders / Dave / Bhaskar) asked to start the loop stops here.

## Gate 2 — Required placeholders filled

The framework ships placeholders written as `<<FILL_ME: ...>>`. Scan the required files for the opening
sentinel `<<FILL_ME:` (a filled file has none; prose must never reproduce that sentinel). The loop must
not run while any remain — scan from the repo root:

    # PowerShell
    Select-String -Path .github/copilot-instructions.md, docs/design.md, `
      .github/skills/build-test.md, .github/skills/build-test-full.md -Pattern '<<FILL_ME:' -SimpleMatch

    # or ripgrep (any shell) — fixed-strings so << is literal
    rg -F -n "<<FILL_ME:" .github/copilot-instructions.md docs/design.md .github/skills/build-test.md .github/skills/build-test-full.md

> **Maintainer note:** inside those four files, always write the token as the bare word `FILL_ME` in
> prose or comments — never the literal opening sentinel — or the scan will match your prose and
> silently re-block the gate.

Required files (must contain no `<<FILL_ME:` sentinel before the loop runs):

- `.github/copilot-instructions.md` — Project profile
- `docs/design.md` — real system design
- `.github/skills/build-test.md` — Dave's gate commands
- `.github/skills/build-test-full.md` — Bhaskar's gate commands

If any match is found, list each file + placeholder and stop:

> "Preflight: N placeholder(s) still need filling before the loop can start: <list>. Fill them
> (see README → How to use, or the `agentify` skill), then re-run."

## Pass

Both gates clean ⇒ proceed to mode selection (trunk ⇒ new-feature, `vibe/<nnn>-*` ⇒ WIP).
