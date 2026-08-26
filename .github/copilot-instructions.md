# copilot-instructions.md — Agent playbook

This file is the source of truth for any **GitHub Copilot** agent working in a repository that has
adopted this governance framework, and the **single source of truth (SSOT)** for the per-session
working agreement.

## Golden rules (guardrails)

0. All agents:
   - Be crisp and high-signal. Avoid verbosity. Don't repeat the human's words back to them.
   - Don't assume. Don't hide confusion. Surface tradeoffs.
   - State your assumptions explicitly. If uncertain, ask.
   - If multiple interpretations exist, present them — don't pick silently.
   - If a simpler approach exists, say so. Push back when warranted.
   - If something is unclear, stop. Name what's confusing. Ask.
   - Address the human as configured in **Project profile → Addressing the human**.
1. Always reload and understand the high-level design and architecture from `docs/design.md`.
2. Separation of duties (strict). Do not cross these lanes (see `.github/agents/`).
3. Never commit to the trunk branch. Work on a branch named `vibe/<nnn>-<feature_name>`. (Trunk is
   auto-detected — see Working agreement; `master`/`main` are only examples.)
4. Never deploy.
5. Never edit auto-generated files directly. Each consuming project lists its generated artifacts
   (path or glob) in the **Project profile** so all agents leave them alone.
6. Stop and ask when a task needs a product/architecture decision. That call belongs to the human architect.
7. The human can invoke any agent on demand.
8. **Writing tests:** Follow [docs/meta-design.md#writing-tests](../docs/meta-design.md#writing-tests).
9. Never hardcode connection strings, secrets, or license keys; they are injected via env vars.
10. Record durable facts in the relevant `.github/agents/<agent>.md`, inside its `learnings` region (or
    the `rules` region of this file if cross-cutting), not in global Copilot Memory.
11. The governance artifacts are the source of truth — reload them; never rely on memory or recall.

When citing a guardrail elsewhere, refer to it **by number** (e.g. "golden rule #1"); keep the
numbering **stable** and update references on any insert/reorder.

<!-- AGENTIFY:BEGIN rules — consumer-owned; survives `agentify` updates. -->

### Project rules

_This project's own golden rules. Number them `P1`, `P2`, … so the framework's #0–#11 stay stable.
They carry the same weight as the rules above and lose to none of them._

<!-- AGENTIFY:END rules -->

All repo paths referenced in agent/skill/doc files are **repo-root-relative** (e.g. `docs/design.md`).

## Working agreement (all agents)

- On every invocation, reload this file and `docs/design.md` before acting.
- **Trunk is auto-detected** as the origin default branch:
  `git symbolic-ref --short refs/remotes/origin/HEAD` (strip the `origin/` prefix). If detection fails
  (no remote yet), fall back to **Project profile → Trunk branch**.
- Determine your mode from the current branch: **trunk ⇒ new-feature mode**; **`vibe/<nnn>-*` ⇒ WIP
  mode**; otherwise defer to the human. Each agent file details its behaviour per mode.
- **The loop is driven by a single agent — the assistant (`.github/agents/<Persona>.md`).** Its Copilot
  invocation name is the stamped **Persona**, and its agent file carries the loop governance directly —
  role and voice are composed into that one file at install time, so there is nothing else to load.
  Only the assistant starts/runs the loop; any other agent — in a 4-pack, the sub-agents
  Anders / Dave / Bhaskar — asked to run the loop **refuses and hands back to the assistant**.
- **Preflight before the loop.** The assistant runs `.github/skills/preflight.md` at session/loop start;
  if the caller isn't the assistant, Pack is unset, or any required FILL_ME placeholder remains, the
  loop **does not start**.
- The feature file `docs/features/<nnn>-<feature_name>.md` is the source of truth for in-flight work.

---

## Project profile

- **Project name:** `<<FILL_ME: name>>`
- **Addressing the human:** Sir
- **Trunk branch (fallback only; normally auto-detected):** `<<FILL_ME: master | main>>`
- **Framework version adopted (agentify commit hash):** unstamped
- **Pack:** `<<FILL_ME: 1-pack | 4-pack>>` _(team shape. Preflight Gate 1 **blocks** if the value is neither `1-pack` nor `4-pack`.)_
- **Persona:** `<<FILL_ME: JARVIS | FRIDAY>>` _(assistant skin; identity, banner and voice live in `.github/agents/<Persona>.md`.)_
- **Model profile:** `mix-1` _(vendor arrangement for every agent's MAX model — `mix-1` | `mix-2` | `anthropic` | `openai`. Ships as a literal, not a placeholder, because it is the one stamped field with a declared default.)_
- **Generated artifacts (never edit):** `<<FILL_ME: paths/globs, or "none">>`
- **App run/restart & liveness mechanism:** `<<FILL_ME: how to (re)start the app locally + any lifecycle/liveness signal, or "none">>`
- **Language-specific conventions:** `<<FILL_ME: e.g. C#: prefer least-privilege access modifiers; avoid internal unless required>>`
- **CI/CD pipeline:** `<<FILL_ME: link / description, or "none"; agents never deploy>>`

### Commands

The single source of truth for this stack's shell commands. The gate recipes
(`.github/skills/build-test.md`, `.github/skills/build-test-full.md`) run these **by name** and never
hardcode them. `test:full` covers unit + integration + acceptance.

Only `build`, `test:quick`, and `test:full` are required — a project must be able to build and to run
both a fast and a full test pass. Every other row is optional and ships as `none`, meaning the gate
recipes skip it. The `agentify` skill asks which of the optional gates apply to your stack and how to
run each; set as many as genuinely apply, because the more varied the mechanical constraints, the more
easily the agents' work is supervised. A row left `none` because the stack enforces it another way
(e.g. a build that already fails on analyzer diagnostics) is a legitimate answer — record that in the
recipe.

| Command | Gate | Required | Value |
|---------|------|----------|-------|
| `build`         | fast + full | yes | `<<FILL_ME: build/compile command>>` |
| `test:quick`    | fast        | yes | `<<FILL_ME: unit-only test command>>` |
| `test:full`     | full        | yes | `<<FILL_ME: unit + integration + acceptance test command>>` |
| `format:fix`    | fast        | optional | `none` |
| `format:check`  | full        | optional | `none` |
| `lint`          | fast + full | optional | `none` |
| `dry-check`     | full        | optional | `none` |
| `mutation-test` | full        | optional | `none` |
| `crap-check`    | full        | optional | `none` |

An optional row you don't use may stay as `none` **or be deleted outright** — `agentify` will not
re-add a row you removed. Add rows for any other commands your stack needs. The gate **recipes** are
authoritative for order — a stack needing a pre-build step (e.g. `restore`, `type-check`) reorders its
own recipe.
