---
name: preflight
description: The pack's loop assistant (`.github/agents/<Persona>.md`) runs this at session/loop start. Enforces two gates before the agentic loop may run — (1) only the pack's assistant runs the loop; (2) no required FILL_ME placeholders remain. Halts and reports otherwise.
---

Run by the pack's loop assistant (`.github/agents/<Persona>.md`) at the start of every session
and before entering the loop. If either gate fails, **do not start the loop** — report and stop.

## Gate 1 — Assistant-only loop (role-based, persona-agnostic)

The loop is driven by a single **assistant** — the agent defined by `.github/agents/<Persona>.md`, one
self-contained file carrying its role governance and its voice. Its Copilot invocation name is the
stamped **Persona** (e.g. JARVIS, FRIDAY); the **name does not gate**. Read **Project profile → Pack**:

- **Pack unset** — "unset" means the Pack value is **not** one of the recognized tokens `1-pack` or
  `4-pack` (line missing, blank, still-unfilled, or any unrecognized string); a recognized token ⇒
  set ⇒ proceed. When unset, stop and ask the human to set it (the `agentify` skill stamps it; no
  default):
  > "Preflight: Project profile → Pack is unset. Set it to 1-pack or 4-pack (via the `agentify` skill), then re-run."

  **Guarantee note:** the framework ships **Pack** as an unfilled `<<FILL_ME: 1-pack | 4-pack>>`
  placeholder, not a valid literal — so a repo that copied the framework without running `agentify`
  fails this gate loudly instead of silently inheriting someone else's pack. `agentify` is still the
  only supported install path, and it rejects any pack ∉ {`1-pack`, `4-pack`}.
- **A non-assistant agent invoked the loop** — in a 4-pack the non-assistant agents are the fixed-named
  **Anders / Dave / Bhaskar**. If the invoker is one of these (or any agent other than the assistant),
  refuse and hand back:
  > "Preflight: the agentic loop is assistant-only. Handing back — please invoke the assistant (`.github/agents/<Persona>.md`)."
- **The assistant invoked** — the agent running `.github/agents/<Persona>.md` proceeds. Its role and
  voice are already composed into that single file, so there is nothing further to load. Identity is
  enforced by the agent file itself, never by the persona name.

**Pack and Persona both ship unfilled; Model profile does not.** `Pack` and `Persona` are no-default —
the human chooses both at install — so the framework ships each as a `<<FILL_ME: …>>` placeholder.
`Pack` is therefore caught by *this* gate, and `Persona` by Gate 2 (the assistant's filename has to
exist, so an unset Persona must block the loop — it did not in earlier versions). `Model profile`
ships as the literal `mix-1` because it *does* have a declared default; that asymmetry is deliberate.

Any agent that is not the assistant stops here.

## Gate 2 — Required placeholders filled

The framework ships placeholders written as `<<FILL_ME: ...>>`. Scan the required files for the opening
sentinel `<<FILL_ME:` (a filled file has none; prose must never reproduce that sentinel). The loop must
not run while any remain — scan from the repo root:

    # PowerShell
    Select-String -Path .github/copilot-instructions.md, docs/design.md, `
      .github/skills/build-test.md, .github/skills/build-test-full.md -Pattern '<<FILL_ME:' -SimpleMatch

    # or ripgrep (any shell) — fixed-strings so << is literal
    rg -F -n "<<FILL_ME:" .github/copilot-instructions.md docs/design.md .github/skills/build-test.md .github/skills/build-test-full.md

> **Maintainer note:** inside those four files — including the **Commands** table in
> `copilot-instructions.md` — always write the token as the bare word `FILL_ME` in prose or comments,
> and keep the literal opening sentinel only in genuinely fillable cells/values. Never reproduce the
> sentinel in prose, or the scan will match your text and silently re-block the gate.

> **Source-vs-consumer note:** the agentify **source/menu** checkout is itself the distributable
> template — it intentionally keeps its consumer `FILL_ME` fields (the adopter's own prompts), so it
> is **expected** to report Gate 2 findings, and its unfilled **Pack** placeholder makes it fail
> **Gate 1** too. That is by construction: the source repo is not loop-capable, and only an
> `agentify`-installed consumer is. Gate 2 is a **consumer-completion** gate, not an install-shape
> check. This is documentation, **not** a bypass — every checkout runs the same scan.

Required files (must contain no `<<FILL_ME:` sentinel before the loop runs):

- `.github/copilot-instructions.md` — Project profile, **including the Commands table** (where the
  per-command placeholders live)
- `docs/design.md` — real system design
- `.github/skills/build-test.md` — the fast-gate recipe (consumer-owned; names commands from the
  Commands table, carries no placeholders — scanned harmlessly)
- `.github/skills/build-test-full.md` — the full-gate recipe (consumer-owned; names commands from
  the Commands table, carries no placeholders — scanned harmlessly)

If any match is found, list each file + placeholder and stop:

> "Preflight: N placeholder(s) still need filling before the loop can start: <list>. Fill them
> (see README → Getting Started, or the `agentify` skill), then re-run."

<!-- AGENTIFY:BEGIN gates — consumer-owned; survives `agentify` updates. -->

## Project gates

_Additional gates this project requires before the loop may run — number them Gate 3 onward. Each must
state its check, its failure message, and whether it halts or merely warns._

<!-- AGENTIFY:END gates -->

## Pass

All gates clean ⇒ proceed to mode selection (trunk ⇒ new-feature, `vibe/<nnn>-*` ⇒ WIP).
