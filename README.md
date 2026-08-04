# agentify

A **project-agnostic agent-governance framework** for **GitHub Copilot**. It packages a battle-tested
hub-and-spoke agentic loop — an orchestrator, a coder, a verifier, and an architect — together with the
guardrails, design process, and skills that keep them in their lanes and shipping in reviewable slices.

Drop it into any repo (via the `agentify` skill), fill in a small **project profile** and
`docs/design.md`, and your Copilot agents inherit a consistent way of working: reload the design, plan
with the architect, implement with the coder, validate with the verifier, and coordinate through the
orchestrator — with the human as the final decision-maker.

> **Scope:** GitHub Copilot only. Roles live as custom agents in `.github/agents/`, rules in
> `.github/copilot-instructions.md`, and reusable procedures in `.github/skills/`.
>
> **Heads-up — this framework is token-heavy** (four agents, a full reload-and-review loop). Swap the
> `model:` in each `.github/agents/*.md` to match your token availability.

## The agentic flow

```
                      ┌───────────────────────────────────────────┐
                      │   Mr. Das  (Human · Iron Man alt)         │
                      │   Final decisions · product/architecture  │
                      │   E2E test · merge to trunk · deploy      │
                      └───────────────────┬───────────────────────┘
                          ▲  decisions    │  requests / "resume,pause"
             escalate     │               ▼
   ┌──────────────────────┴───────────────────────────────────────────────────────┐
   │                         J A R V I S   (orchestrator)                         │
   │                          .github/agents/jarvis.md                            │
   │   hub-and-spoke loop coordinator · owns git + the task file · never codes    │
   │   Mode select:  branch==trunk ─► NEW FEATURE   │   branch==vibe/* ─► WIP      │
   └───┬───────────────────┬─────────────────────────┬───────────────────────┬────┘
       │ design            │ implement (uncommitted) │ verify                │ review
       ▼                   ▼                         ▼                       ▼
 ┌───────────┐       ┌───────────┐            ┌───────────┐           ┌───────────┐
 │  ANDERS   │       │   DAVE    │            │  BHASKAR  │           │  ANDERS   │
 │ architect │       │  coder    │            │ verifier  │           │ (design   │
 │anders.md  │       │ dave.md   │            │bhaskar.md │           │  review)  │
 │ no code   │       │ no commit │            │ no code   │           │ no commit │
 │ no build  │       │ no push   │            │ no commit │           │           │
 └───────────┘       └───────────┘            └───────────┘           └───────────┘
       │                   │                        │                       │
       └──── all agents hand back to JARVIS ────────┴───────────────────────┘

  PER-TASK LOOP (WIP mode):
    0. JARVIS   preflight: JARVIS-only + no FILL_ME left, else STOP
    1. JARVIS ─► DAVE      implement one deployable slice, leave UNCOMMITTED
    2. JARVIS ─► BHASKAR   validate build + unit + integration + UI gates
    3. fail? ─► DAVE fixes ─► back to 2   (repeat until green)
    4. JARVIS ─► ANDERS    final design review (APPROVE / w-SUGGESTIONS / CONCERNS)
    5. blocking concern?   ─► escalate to Mr. Das
    6. JARVIS  commit vibe/<nnn>-<feature> ─► push ─► PR ─► restart app
    7. next task ─► back to 1        (no more tasks ─► Mr. Das: PR approve + merge)

  ┌─ GUARDRAILS · .github/copilot-instructions.md ───────────────────────────────┐
  │ strict lanes · never touch trunk · vibe/<nnn>-<feature> · never deploy        │
  │ never edit generated files (Project profile) · no hardcoded secrets · ask     │
  │ docs/design.md   system design map (fill in before the loop — preflight-gated)│
  └──────────────────────────────────────────────────────────────────────────────┘

  ┌─ COORDINATION ARTIFACTS (JARVIS lane) ─────────┐   ┌─ CODE (Dave lane) ─────────────────┐
  │ docs/features/<nnn>-<feature>.md  task file/SoT │   │ your source layout, per            │
  │ docs/features/TASK_FILE_TEMPLATE.md  template   │   │ docs/design.md                     │
  │   Options ▸ Slices ▸ Tasks ▸ Risks ▸ Notes      │   │ tests alongside (unit + integ)     │
  └─────────────────────────────────────────────────┘   │ generated files: never edit        │
                                                         └────────────────────────────────────┘

  ┌─ LOCAL RUNTIME (optional · not an agent) ──────────────────────────────────────┐
  │ project-defined run/liveness mechanism (Project profile → App run/restart).     │
  │ after each task commit, restart or you run a stale binary.                       │
  │ .github/skills/  build-test · build-test-full · preflight                        │
  └────────────────────────────────────────────────────────────────────────────────┘

  ┌─ SHIP (Mr. Das) ───────────────────────────────────────────────────────────────┐
  │ PR ─► CI/CD pipeline ─► Staging ─► [manual approval] ─► Production               │
  │ (agents never deploy; the human merges to trunk and ships)                      │
  └────────────────────────────────────────────────────────────────────────────────┘
```

## Contents

- `AGENTS.md` — top-level pointer into `.github/` governance.
- `.github/copilot-instructions.md` — golden rules + working agreement + **Project profile** (the SSOT).
- `.github/agents/` — `jarvis` (orchestrator), `anders` (architect), `dave` (coder), `bhaskar` (verifier).
- `.github/skills/` — `agentify` (install/update), `preflight` (loop guard), `retrospective`,
  `build-test`, `build-test-full`.
- `docs/` — `design.md` (fill in), `meta-design.md` (how features are designed), `backlog.md`,
  `features/TASK_FILE_TEMPLATE.md`.
- `LICENSE` — MIT.
- Plumbing — `.editorconfig`, `.gitignore`, `.gitattributes`, `.vscode/` (generic starters; adjust per stack).

## The model in one minute

- **Separation of duties is strict.** Only Dave writes code; only Bhaskar validates; Anders designs
  and reviews; JARVIS coordinates and owns git. The human decides.
- **The loop is JARVIS-only** and **preflight-gated**: it won't start unless JARVIS runs it and every
  required placeholder is filled.
- **Work happens on `vibe/<nnn>-<feature_name>` branches**, never on the (auto-detected) trunk. No agent deploys.
- **Features are sliced** into independently deployable, end-to-end-verifiable increments; each has a
  doc under `docs/features/`.
- **The loop:** JARVIS → Dave (implement) → Bhaskar (verify) → Anders (review) → human gate → commit/PR.

## How to use this repo

1. **Install** into your target repo with the **`agentify` skill** (`.github/skills/agentify.md`): it
   copies `.github/`, `docs/`, `AGENTS.md`, and plumbing, then **stamps the adopted framework version
   (this repo's commit hash)**    into your Project profile. (Manual copy is the fallback — remember `AGENTS.md`, and paste this repo's
   commit hash into the profile's *Framework version adopted* line.)
2. **Fill the placeholders.** Complete every `<<FILL_ME: ...>>` in `.github/copilot-instructions.md`
   (Project profile) and write your real `docs/design.md`. The `preflight` skill blocks the loop until
   these are done.
3. **Fill the skill templates** `build-test.md` and `build-test-full.md` with your stack's
   build/test/lint commands.
4. **Invoke the agents in Copilot** by name (Anders, Dave, Bhaskar, JARVIS). Start a feature by asking
   JARVIS; it runs preflight, defers to Anders for design, then runs the loop.

## For consumer agents

The **canonical** per-session rules live in `.github/copilot-instructions.md` (the SSOT: golden rules
#0–#10 + the Working agreement). Non-authoritative summary: every agent reloads
`.github/copilot-instructions.md` + `docs/design.md` first; picks mode from the branch (trunk ⇒
new-feature, `vibe/<nnn>-*` ⇒ WIP); stays in its lane per `.github/agents/<you>.md`; treats
`docs/features/<nnn>-*.md` as the source of truth; and hands back to JARVIS. **Only JARVIS runs the
loop**, and only after preflight passes. See the SSOT for the authoritative text.

## Versioning

The framework version is the **agentify commit hash**. The `agentify` skill stamps it into each
consumer's Project profile (*Framework version adopted*) on install/update, so you always know which
revision a repo adopted and can re-run the skill to pull updates.

## Retrospective

Governance improves itself. After roughly every 5 completed features, JARVIS reminds the human to run
the `retrospective` skill: Anders distills durable, cross-cutting learnings; the human approves any
guardrail change; Dave applies the minimal edit. See `.github/skills/retrospective.md`.

## TODO — additional loop constraints

Planned quality gates to wire into the loop as skills the verifier enforces on every change:

- [ ] **TBD** — additional guardrails for **DRY** (duplication), **mutation testing** (tests actually
      assert behaviour), and **CRAP metrics** (complexity × coverage), plus perf/security gates —
      per each consumer's stack.
