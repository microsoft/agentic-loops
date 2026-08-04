# copilot-instructions.md — Agent playbook

This file is the source of truth for any **GitHub Copilot** agent working in a repository that has
adopted this governance framework, and the **single source of truth (SSOT)** for the per-session
working agreement. It is intentionally project-agnostic: anything stack- or product-specific lives in
the **Project profile** at the bottom, which each consuming repo fills in.

Address the human as configured in **Project profile → Addressing the human** (default: "Sir").

**Path convention:** all repo paths referenced in agent/skill/doc files are **repo-root-relative**
(e.g. `docs/design.md`, `.github/agents/jarvis.md`).

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
10. Record durable facts in the relevant `.github/agents/<agent>.md` (or this file if cross-cutting),
    not in global Copilot Memory.

When citing a guardrail elsewhere, refer to it **by number** (e.g. "golden rule #1"); keep the
numbering **stable** and update references on any insert/reorder.

## Working agreement (all agents)

- On every invocation, reload this file and `docs/design.md` before acting.
- **Trunk is auto-detected** as the origin default branch:
  `git symbolic-ref --short refs/remotes/origin/HEAD` (strip the `origin/` prefix). If detection fails
  (no remote yet), fall back to **Project profile → Trunk branch**.
- Determine your mode from the current branch: **trunk ⇒ new-feature mode**; **`vibe/<nnn>-*` ⇒ WIP
  mode**; otherwise defer to the human. Each agent file details its behaviour per mode.
- **The loop is JARVIS-only.** Only JARVIS starts/runs the agentic loop. If you are any other agent and
  are asked to orchestrate/start the loop, **refuse and hand back to JARVIS**.
- **Preflight before the loop.** JARVIS runs `.github/skills/preflight.md` at session/loop start; if the
  caller is not JARVIS, or any required `FILL_ME` placeholder remains unfilled, the loop **does not start**.
- The feature file `docs/features/<nnn>-<feature_name>.md` is the source of truth for in-flight work.

---

## Project profile (each consuming repo fills this in)

> Replace every `FILL_ME` placeholder below when you adopt this framework (the `agentify` skill
> stamps some of these). Preflight blocks the loop until required placeholders are filled. Keep it
> short. Delete this note once every placeholder is filled.

- **Project name:** `<<FILL_ME: name>>`
- **Addressing the human:** Sir  _(example: "Mr. Das")_
- **Trunk branch (fallback only; normally auto-detected):** `<<FILL_ME: master | main>>`
- **Framework version adopted (agentify commit hash):** unstamped _(auto-stamped by the `agentify` skill; manual copy: paste this repo's commit hash)_
- **Generated artifacts (never edit):** `<<FILL_ME: paths/globs, or "none">>`
- **App run/restart & liveness mechanism:** `<<FILL_ME: how to (re)start the app locally + any lifecycle/liveness signal, or "none">>`
- **Build/test skills:** `.github/skills/build-test.md` (fast, Dave) and
  `.github/skills/build-test-full.md` (full + lint, Bhaskar) — fill in the actual commands for your stack.
- **Language-specific conventions:** `<<FILL_ME: e.g. C#: prefer least-privilege access modifiers; avoid internal unless required>>`
- **CI/CD pipeline:** `<<FILL_ME: link / description; agents never deploy>>`
