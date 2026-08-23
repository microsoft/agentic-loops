# Feature: Agent naming overhaul + persona-named loop binder
**Branch:** vibe/003-agent-naming-and-persona-binder
**Status:** In Progress

## Requirements

Three human-directed changes to the framework's agent terminology and the invokable-agent surface:

1. **Uniformly drop the words "driver" / "orchestrator".** The loop **agent** (the invokable binder)
   is named **assistant**.
2. **Keep a distinct label for the 4-pack ROLE** (formerly "orchestrator"), so the role body is not
   confused with the agent. The human chose **conductor**.
3. **The loop agent must appear under its chosen persona** (e.g. **JARVIS** / **FRIDAY**) in the human's
   agent list — not under a filename stem like "driver"/"assistant". Observed: after installing the
   framework in another repo, the agent list shows "driver" (the binder **filename**), not the persona.

Constraints from the human:
- Persona binder filenames are **all-caps, MCU-style** (`JARVIS.md`, `FRIDAY.md`).
- A neutral copy-source template may keep a neutral name **as long as it never appears in the agent list**.

## Design Options (Ox)

Root cause (confirmed): the VS Code custom-agent surface lists an agent by its **filename stem**, while
the Copilot CLI `/agent` picker honors frontmatter `name`. So stamping `name: <persona>` fixes only the
CLI; the **file itself** must be named after the persona. Two orthogonal problems:
**P1** — the installed binder file must be `agents/<Persona>.md`; **P2** — the framework's prose
references to the binder path must not go stale (note: none is a runtime load-by-path; all are
descriptive prose, so P2 is an accuracy fix, not a correctness fix).

### O1 — persona-named binder (A) + generic references (C)  ✅ RECOMMENDED (Anders)
- `agentify` installs the binder as `agents/<Persona>.md` (stamped `name`); all collateral binder-path
  prose becomes generic/persona-relative (no fragile per-install find/replace).
- Pros: fixes P1 correctly; durable for future personas; DRY. Cons: a neutral copy-source is needed.

### O2 — O1 + self-dogfood
- Additionally rename this repo's own binder to `agents/JARVIS.md`. Pros: full dogfooding. Cons: the
  canonical template becomes persona-named (mild smell / maintainer trap).

### O3 — pure generic references (C only) ❌
- Rejected: does not rename the file, so VS Code still shows the neutral name — bug unfixed.

### O4 — rewrite all refs at install time ❌
- Rejected: fragile find/replace across files on every install/update; C makes it unnecessary.

**Recommended: O1**, with the human's variant (see Notes & Decisions D1–D3): the neutral copy-source
template is **moved out of `.github/agents/`** (to `.github/agent-templates/binder.md`) so it is never
surfaced as an invokable agent, and persona binder filenames are **all-caps**.

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1 | Uniform `driver`/`orchestrator` → **assistant** across the framework (the loop agent). | - |
| S2 | Split the 4-pack **role** back out to a distinct label **conductor** (agent stays "assistant"). | S1 |
| S3 | Install the binder as `agents/<PERSONA>.md` (all-caps); hide the neutral template outside `agents/`; make binder-path references generic; add an Update cleanup for legacy binders. | S2 |

## Tasks (Tx)

| #  | Slice | Task | Status | Commit |
|----|-------|------|--------|--------|
| T1 | S1 | `git mv agents/driver.md → agents/assistant.md`, `agent-roles/orchestrator.md → assistant.md`; replace all `driver`/`orchestrator` tokens with `assistant` (keep the verb "driven"). | Done | 1 |
| T2 | S2 | `git mv agent-roles/assistant.md → conductor.md`; convert **orchestrator-origin** occurrences to `conductor` (role selectors, role-body self-refs, agentify `$role`, README role listing); leave **driver-origin** `assistant` intact. | Done | 2 |
| T3 | S3 | `git mv agents/assistant.md → .github/agent-templates/binder.md` (neutral, not surfaced); document its role in agentify.md + README (no in-file header comment — it would travel into consumer agent files). | Done | 3 |
| T4 | S3 | `agentify` Install: copy template → `agents/<PERSONA>.md` (`$persona.ToUpper()`); stamp `name`/`description`; add persona⇄sub-agent collision guard + framework-stem reservation. | Done | 3 |
| T5 | S3 | `agentify` Update: identify the existing binder (lone `agents/*.md` stem ∉ {anders,dave,bhaskar}); refresh into `agents/<PERSONA>.md`; `git rm` legacy `driver.md`/`assistant.md`; idempotent. | Done | 3 |
| T6 | S3 | Make the binder-path references generic (`.github/agents/<Persona>.md`) in README, copilot-instructions, preflight, conductor.md, solo.md, personas/*, and reword the agentify ownership/copy-set prose. | Done | 3 |
| T7 | S3 | Record the feature-001 reversal decision + the naming glossary in this doc. | Done | 3 |

## Risks (Rx)

- **R1** — an already-installed consumer keeps a stale `driver.md`/`assistant.md`, lingering as a second
  invokable agent with the wrong name. Mitigation: Update cleanup (T5).
- **R2** — a persona name collides with a sub-agent filename (`anders`/`dave`/`bhaskar`) or a framework
  stem. Mitigation: install-time collision guard + reservation (T4).
- **R3** — CLI shows `JARVIS` (frontmatter) while VS Code shows the filename; with all-caps filenames
  these now match. Accepted/documented.

## Assumptions (Ax)

- **A1** — VS Code keys the agent list on the **filename stem**; the CLI `/agent` picker on frontmatter
  `name` (evidence: CLI lists "JARVIS"; VS Code lists the filename).
- **A2** — No binder-path reference is a runtime load-by-path; all are descriptive prose (verified).
- **A3** — Persona binder filenames are all-caps; persona **overlay** files in `.github/personas/`
  remain lower-case (existing convention; not surfaced as agents).

## Deferrals (Dx)

- **D1** — Optional preflight hardening: warn if more than one candidate binder is present in
  `.github/agents/`.
- **D2** — Hosting persona/template files at a dist/template path (inherits feature-001 D4).

## Notes & Decisions

- **D1 (human) — filename case:** persona binder filenames are **all-caps MCU-style** (`JARVIS.md`,
  `FRIDAY.md`). Frontmatter `name` stays the persona value, so CLI and VS Code agree.
- **D2 (human) — neutral template is hidden:** the copy-source template lives at
  `.github/agent-templates/binder.md`, **outside `.github/agents/`**, so it is never listed as an
  invokable agent. (The human: "you can keep this name … as long as this isn't shown to me when I am
  listing agents.")
- **D3 (human) — no self-dogfood (O1, not O2):** this framework repo is **not** given its own
  persona-named binder; its `.github/agents/` holds only the sub-agents. The loop is gated off here
  anyway (source repo; FILL_ME Commands), so no invokable binder is needed.
- **Feature-001 reversal.** 001 chose "a single thin binder `agents/driver.md`; `agents/jarvis.md` /
  `agents/friday.md` ABSENT (flavor → `personas/`, role bodies → `agent-roles/`)". This feature
  supersedes only the **filename** half: the installed binder is now `agents/<PERSONA>.md`, because the
  VS Code surface keys on filename, not frontmatter `name`. **Preserved from 001:** the binder is still
  one thin, persona-neutral shell (now a template); flavor still lives in `personas/`; role bodies still
  in `agent-roles/`; exactly one binder per repo. The new `agents/JARVIS.md` is the **binder shell**
  whose filename equals the persona — **not** the pre-001 role-carrying agent; bodies are identical
  across personas modulo frontmatter `name`.
- **Naming glossary (post-feature):** **assistant** = the loop **agent** concept / binder shell;
  **conductor** = the 4-pack **role** body (`agent-roles/conductor.md`); **solo** = the 1-pack role body;
  the invokable binder installs as `agents/<PERSONA>.md` and surfaces under the persona.
- Committed as three separate commits (one per slice), per the human's request. PR held pending human
  go-ahead.
