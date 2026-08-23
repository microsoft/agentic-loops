# agentify

A **project-agnostic agent-governance framework for GitHub Copilot**: a hub-and-spoke agentic loop —
conductor, coder, verifier, architect (or a single solo generalist) — plus the guardrails and skills
that keep them in their lanes, shipping reviewable slices. Drop it into any repo with the `agentify`
skill, fill a small **Project profile** and `docs/design.md`, and your Copilot agents inherit a
consistent way of working — the human stays final decision-maker.

## Getting Started

1. **Install** — run the `agentify` skill (`.github/skills/agentify.md`) from an agentify checkout,
   pointed at your target repo (on a `vibe/*` branch, never trunk).
2. **Choose a pack *and* a persona** when asked — both no-default; the persona is your assistant's skin
   (menu in `.github/personas/`; see *Install options*).
3. **Fill the placeholders** — every `<<FILL_ME: ...>>` in `.github/copilot-instructions.md` (the
   Project profile, **including the Commands table**) and replace `docs/design.md` with your real
   design. Preflight blocks the loop until no placeholder remains.
4. **Invoke the assistant** by its persona name (you chose it at install — e.g. **JARVIS** or **FRIDAY**); a
   4-pack also exposes **Anders / Dave / Bhaskar**. Ask the assistant to start a feature; it prints its
   banner, runs preflight, then loops.

## Install options

The full 4-agent loop is **token-heavy**, and many projects don't need it — so the `agentify` skill
**asks which pack** (no default). **Persona is a mandatory, no-default overlay, decoupled from pack**:
pick any skin in `.github/personas/` for your assistant (a persona may not match the pack's natural assistant —
that's allowed). Roles live in `.github/agent-roles/`, personas in `.github/personas/`, and the thin
binder is `.github/agents/<Persona>.md`. Note: stronger and more varied Commands ⇒ agents are more easily
supervised.

### 1-pack (solo)
Ships the **solo** role — one generalist who does design → implement → verify → review, plus git
and the task file. **Separation of duties is waived.** Lightest on tokens; best for small or low-stakes
repos.

### 4-pack (full team)
Ships the **conductor** role **+ Anders / Dave / Bhaskar** — strict separation of duties and
the full hub-and-spoke loop. Token-heavy; best for larger or higher-stakes work.

## The model

```
Human — decides · E2E-tests · merges · deploys
   ▲ escalate                    │ requests
   │                             ▼
 Assistant = agents/<Persona>.md   (skin: Persona · role: Pack)
   ├─ 4-pack → role: conductor — owns git + task file; never codes:
   │     ├─► Anders   design / review
   │     ├─► Dave     implement (leave uncommitted)
   │     └─► Bhaskar  verify (build + tests + gates)
   └─ 1-pack → role: solo — does all of the above solo (duties merged)

 Session start: assistant prints its persona banner, then preflight (once).
 Per task: Dave → Bhaskar (till green) → Anders → commit vibe/<nnn> → PR.
 Escalate blockers immediately; pause at a slice boundary if needed.
 Never trunk. Never deploy.
```

- **Separation of duties** is strict in the 4-pack; merged into one agent in the 1-pack.
- **The loop is single-assistant and preflight-gated** — only the assistant (`.github/agents/<Persona>.md`) runs
  it; it won't start until preflight passes with all placeholders filled.
- **Features are sliced** into independently deployable, end-to-end-verifiable increments.

## Layout

- `AGENTS.md` — top-level pointer into `.github/` governance.
- `.github/copilot-instructions.md` — golden rules + working agreement + **Project profile** (the SSOT,
  including the Commands table).
- `.github/agents/` — `anders` / `dave` / `bhaskar` (4-pack sub-agents); the loop binder installs here per persona as `<Persona>.md`.
- `.github/agent-templates/` — `binder.md`, the loop-binder template (installed as `.github/agents/<Persona>.md`).
- `.github/agent-roles/` — `conductor` (4-pack) / `solo` (1-pack) role bodies (no frontmatter).
- `.github/personas/` — assistant skins `jarvis` / `friday` (no frontmatter).
- `.github/skills/` — `agentify` (install/update), `preflight` (loop guard), `retrospective`,
  `build-test` + `build-test-full` (the gate recipes).
- `docs/` — `design.md` (fill in), `meta-design.md`, `backlog.md`, `features/TASK_FILE_TEMPLATE.md`.
- `LICENSE` (MIT) + plumbing (`.editorconfig`, `.gitignore`, `.gitattributes`, `.vscode/`).

## Commands

The consumer fills a **Commands** table in the Project profile (the SSOT); the two gate-skills reference
it by name. **Required core:** `build`, `lint`, `format:fix` + `format:check`, `test:quick`, `test:full`.
**Optional gates:** `dry-check`, `mutation-test`, `crap-check` — ship as `none`; set a command to enable
one. Add rows for any other commands your stack needs; fill in as many as apply — stronger, more varied
constraints ⇒ agents are more easily supervised.

## Versioning

The framework version is the **agentify commit hash**; the `agentify` skill stamps it (with the chosen
pack + persona) into each consumer's Project profile on install/update, so you know which revision a repo
adopted and can re-run to pull updates.

## Retrospective

After roughly every 5 features, the assistant reminds the human to run the `retrospective` skill: the
architect distills durable, cross-cutting learnings, the human approves any guardrail change, and the
coder applies the minimal edit — the solo assistant does all three in a 1-pack. See
`.github/skills/retrospective.md`.

## For consumer agents

Canonical per-session rules live in `.github/copilot-instructions.md` (golden rules #0–#11 + Working
agreement) — this README is non-authoritative. Reload it and `docs/design.md`, pick mode from the branch,
stay in your lane, and treat `docs/features/<nnn>-*.md` as the SoT.
