# copilot-instructions.md — Agent playbook

This file is the source of truth for any **GitHub Copilot** agent working in a repository that has
adopted this governance framework, and the **single source of truth (SSOT)** for the per-session
working agreement. It is intentionally project-agnostic: anything stack- or product-specific lives in
the **Project profile** at the bottom, which each consuming repo fills in.

Address the human as configured in **Project profile → Addressing the human** (default: "Sir").

**Path convention:** all repo paths referenced in agent/skill/doc files are **repo-root-relative**
(e.g. `docs/design.md`, `.github/agent-roles/conductor.md`).

## Golden rules (guardrails)

0. All agents:
   - Be crisp and high-signal. Avoid verbosity. Don't repeat the human's words back to them.
   - Don't assume. Don't hide confusion. Surface tradeoffs.
   - State your assumptions explicitly. If uncertain, ask.
   - If multiple interpretations exist, present them — don't pick silently.
   - If a simpler approach exists, say so. Push back when warranted.
   - If something is unclear, stop. Name what's confusing. Ask.
1. Always reload and understand the high-level design and architecture from `docs/design.md`.
2. Separation of duties (strict). Do not cross these lanes (see `.github/agents/`).
3. Never commit to the trunk branch. Work on a branch named `vibe/<nnn>-<feature_name>`. (Trunk is
   auto-detected — see Working agreement; `master`/`main` are only examples.)
4. Never deploy.
5. Never edit auto-generated files directly. Each consuming project lists its generated artifacts
   (path or glob) in the **Project profile** so all agents leave them alone.
6. Stop and ask when a task needs a product/architecture decision. That call belongs to the human architect.
7. The human can invoke any agent on demand.
8. Testing philosophy (stack-agnostic):
   - Definitely write unit tests for business logic.
   - Add integration tests for critical paths / cross-component scenarios — not every code path. Don't overdo.
   - Some tests for deep UI components / rendering are okay (when a UI exists), but don't overdo.
   - Avoid tests that cause timing/flakiness issues.
9. Never hardcode connection strings, secrets, or license keys; they are injected via env vars.
10. Record durable facts in the relevant `.github/agents/<agent>.md` or `.github/agent-roles/<role>.md`
    (or this file if cross-cutting), not in global Copilot Memory.
11. The governance artifacts are the source of truth — reload them; never rely on memory or recall.

When citing a guardrail elsewhere, refer to it **by number** (e.g. "golden rule #1"); keep the
numbering **stable** and update references on any insert/reorder.

## Working agreement (all agents)

- On every invocation, reload this file and `docs/design.md` before acting.
- **Trunk is auto-detected** as the origin default branch:
  `git symbolic-ref --short refs/remotes/origin/HEAD` (strip the `origin/` prefix). If detection fails
  (no remote yet), fall back to **Project profile → Trunk branch**.
- Determine your mode from the current branch: **trunk ⇒ new-feature mode**; **`vibe/<nnn>-*` ⇒ WIP
  mode**; otherwise defer to the human. Each agent file details its behaviour per mode.
- **The loop is driven by a single agent — the assistant (`.github/agents/<Persona>.md`).** Its Copilot
  invocation name is the stamped **Persona** and its role is set by **Pack** (`4-pack ⇒ conductor`,
  `1-pack ⇒ solo`). Only the assistant starts/runs the loop; any other agent — in a 4-pack, the sub-agents
  Anders / Dave / Bhaskar — asked to run the loop **refuses and hands back to the assistant**.
- **Preflight before the loop.** The assistant runs `.github/skills/preflight.md` at session/loop start; if
  the caller isn't the assistant, Pack is unset, or any required FILL_ME placeholder remains, the loop
  **does not start**.
- The feature file `docs/features/<nnn>-<feature_name>.md` is the source of truth for in-flight work.

---

## Project profile (each consuming repo fills this in)

> Replace every `FILL_ME` placeholder below when you adopt this framework (the `agentify` skill
> stamps some of these). Preflight blocks the loop until required placeholders are filled. Keep it
> short. Delete this note once every placeholder is filled.
>
> Fields are of three kinds: **Stamped literals** (`Framework version`, `Pack`, `Persona` — set by the `agentify` skill, never placeholders), **Manual fields** (the ones below still showing the FILL_ME sentinel, which Gate-2 scans and which block the loop until filled), and **defaults** (e.g. Addressing = Sir). Only Manual fields gate the loop.

- **Project name:** `<<FILL_ME: name>>`
- **Addressing the human:** Sir  _(example: "Mr. Das")_
- **Trunk branch (fallback only; normally auto-detected):** `<<FILL_ME: master | main>>`
- **Framework version adopted (agentify commit hash):** unstamped _(auto-stamped by the `agentify` skill; manual copy: paste this repo's commit hash)_
- **Pack:** `4-pack` _(role selector: `4-pack ⇒ conductor`, `1-pack ⇒ solo`; the `agentify` skill asks and stamps this — no default. Preflight Gate-1 **blocks** if unset, i.e. the value isn't `1-pack`/`4-pack`.)_
- **Persona:** JARVIS _(assistant skin; the `agentify` skill asks and stamps this — no default, not a preflight blocker. Menu = the overlays in `.github/personas/`, today JARVIS | FRIDAY.)_
- **Generated artifacts (never edit):** `<<FILL_ME: paths/globs, or "none">>`
- **App run/restart & liveness mechanism:** `<<FILL_ME: how to (re)start the app locally + any lifecycle/liveness signal, or "none">>`
- **Build/test skills:** `.github/skills/build-test.md` (fast, Dave) and
  `.github/skills/build-test-full.md` (full, Bhaskar) are framework-owned recipes that run the commands
  named in the **Commands** table below — fill that table in for your stack.
- **Language-specific conventions:** `<<FILL_ME: e.g. C#: prefer least-privilege access modifiers; avoid internal unless required>>`
- **CI/CD pipeline:** `<<FILL_ME: link / description; agents never deploy>>`

### Commands

> The consumer's stack commands (the `agentify` skill prompts for these). Each Value is a shell command.
> Optional rows **ship as `none`** and are skipped by the gate recipes; set a real command to enable
> that gate. **Required** rows must be a real command — never `none`; only the optional rows may be
> `none`. The stronger and more varied these constraints, the more easily your agents can be
> supervised, so fill in as many as apply. (Maintainer note: in prose write the bare word FILL_ME; the
> literal sentinel appears only in the required Value cells below, which preflight scans.)

| Command | Gate | Required | Value |
|---------|------|----------|-------|
| `build`         | fast + full   | yes | `<<FILL_ME: build/compile command>>` |
| `lint`          | fast + full   | yes | `<<FILL_ME: lint command>>` |
| `format:fix`    | fast (Dave)   | yes | `<<FILL_ME: format command that WRITES changes>>` |
| `format:check`  | full (Bhaskar)| yes | `<<FILL_ME: format command that only CHECKS>>` |
| `test:quick`    | fast (Dave)   | yes | `<<FILL_ME: unit-only test command>>` |
| `test:full`     | full (Bhaskar)| yes | `<<FILL_ME: unit + integration test command>>` |
| `dry-check`     | full          | optional | `none` |
| `mutation-test` | full          | optional | `none` |
| `crap-check`    | full          | optional | `none` |

Add rows for any other commands your stack needs — the gate recipes run them **after** the core steps;
a stack needing a **pre-build** step (e.g. `restore`, `type-check`) should reorder its own recipe (the
recipe is authoritative for order).
