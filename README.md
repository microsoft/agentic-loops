# agentify

A **project-agnostic agent-governance framework for GitHub Copilot**: guardrails + skills that keep a
hub-and-spoke loop in-lane, shipping reviewable slices. Self-learning, with a hands-free mode for the
4-pack — conductor · coder · verifier · architect — or a solo generalist.

The human designs the lanes, guardrails & constraints and stays final decision-maker.

## Packs

`agentify` asks which **pack** and which **persona** at install (both no-default; persona = your
assistant's skin, from `.github/agent-templates/personas/`).

| Pack | Makeup | Separation of duties | Tokens | Use when |
|------|--------|----------------------|--------|----------|
| **1-pack** (solo) | one generalist — designs · implements · verifies · reviews; owns git + task file | merged (waived) | lightest | small / low-stakes repos, fast iteration |
| **4-pack** (team) | conductor **+** Anders (architect) · Dave (coder) · Bhaskar (verifier) | strict, hub-and-spoke lanes | heavy | larger / higher-stakes work wanting independent review |

## The loops

**① Hands-free loop — WIP mode** (the day-to-day engine)

```
  ① HANDS-FREE LOOP · WIP mode — one task at a time, on reasonable assumptions

      ┌───────────────────────────────────────────────────┐
      │  Human — decides · E2E-tests · merges · deploys   │
      └───────────────────────────────────────────────────┘
          │ requests              ▲ escalate anytime
          ▼                       │
      ┌───────────────────────────────────────────────────┐
   ┌─►│  Assistant · conductor — owns git + task file     │
   │  └───────────────────────────────────────────────────┘
   │      │ hands off one task
   │      ▼
   │  ┌───────┐  fail ↔ fix  ┌─────────┐  green  ┌────────┐
   │  │  Dave │◄────────────►│ Bhaskar │────────►│ Anders │
   │  │  code │              │  verify │         │ review │
   │  └───────┘              └─────────┘         └────────┘
   │  uncommitted       build + tests + gates     design
   │                                               │ pass
   │                                               ▼
   │  ┌───────────────────────────────────────────────────┐
   └──┤ commit vibe/<nnn> · push · PR · restart app       │
      └───────────────────────────────────────────────────┘
   ↺ next task

  slice end → pause only if sign-off needed · never trunk · never deploy
```

**② Design session — new-feature mode** → Slices · Tasks · OSTRAD

```
  ② DESIGN SESSION · new-feature mode — Anders leads with the human (no hints)

   ┌────────┐   requirements   ┌───────────┐   writes   ┌──────────────────────────┐
   │ Human  │─────────────────►│   Anders  │───────────►│ docs/features/<nnn>.md   │
   │        │◄─────────────────│ architect │            ├──────────────────────────┤
   └────────┘   options · recs └───────────┘            │ O  Options  → pick one   │
  approves the design                                   │ S  Slices                │
                                                        │ T  Tasks                 │
                                                        │ R  Risks                 │
                                                        │ A  Assumptions           │
                                                        │ D  Deferrals             │
                                                        └──────────────────────────┘

  Feature ─► Slices (independently shippable · E2E-verifiable) ─► Tasks (1+ each) ─► loop ①
```

**③ Retro loop — self-learning** (every ≥ 5 features)

```
  ③ RETRO LOOP · self-learning — every ≥ 5 features (count-based)

   ┌───────────┐    ┌──────────┐    ┌────────┐    ┌────────┐
   │ Assistant │───►│  Anders  │───►│ Human  │───►│  Dave  │
   │  reminds  │    │ distills │    │ okays  │    │applies │
   └───────────┘    └──────────┘    └────────┘    └────────┘
        ▲                                                │
        └───────────── updated guardrails ↺ ─────────────┘
          (1-pack: the solo assistant fills all three roles)
```

The assistant reminds the human; run the loop with the `retrospective` skill (`.github/skills/retrospective.md`).

## Getting Started

1. **Install** — run the `agentify` skill (`.github/skills/agentify.md`) from an agentify checkout,
   pointed at your target repo (on a `vibe/*` branch, never trunk).
2. **Choose a pack *and* a persona** when asked — both no-default (see *Packs* above).
3. **Answer the interview.** `agentify` walks your Project profile and your gate commands rather than
   leaving you a wall of placeholders: what to build/test with (`build`, `test:quick`, `test:full` are
   required), which optional gates your stack actually has and how to run each (`format:fix`/`check`,
   `lint`, `dry-check`, `mutation-test`, `crap-check` — every one may be `none`), and whether the
   project even has a local run/restart & liveness mechanism (many don't). Stronger, more varied
   constraints ⇒ agents are more easily supervised.
4. **Replace `docs/design.md`** with your real design — the one thing the interview can't write for
   you. Preflight blocks the loop until it and every other placeholder is filled.
5. **Invoke the assistant** by its persona name (e.g. **JARVIS** / **FRIDAY**); a 4-pack also exposes
   **Anders / Dave / Bhaskar**. Ask it to start a feature — it prints its banner, runs preflight, then loops.

> **Versioning** — `agentify` stamps its commit hash (the framework version) plus your pack, persona,
> and model profile into the Project profile on install/update; re-run the skill to pull framework
> updates. Updates recompose your assistant and refresh the framework files, but never touch your
> Project profile, your gate recipes, your design docs, or any **consumer extension region** — the
> named `AGENTIFY:BEGIN`/`END` blocks framework-owned files ship with so you can add your own golden
> rules, agent rules, preflight gates, and testing mechanism without losing them. If you edit
> framework prose *outside* a region, the next update shows you that diff and asks before overwriting.

## The model

- **Single-assistant, preflight-gated** — only the assistant (`.github/agents/<Persona>.md`) runs the
  loop; it is one self-contained file with no role/persona indirection, and it won't start until
  preflight passes with every placeholder filled.
- **Features are sliced** into independently deployable, end-to-end-verifiable increments.
- **Model profile** — `agentify` sets each agent's MAX model at `reasoning: max`; pick an arrangement:

  | Profile | Designs + codes | Verifies + drives |
  |---------|-----------------|-------------------|
  | **`mix-1`** *(default · 1st choice)* | Opus 5 | GPT-5.6 Sol |
  | **`mix-2`** *(2nd choice)* | GPT-5.6 Sol | Opus 5 |
  | `anthropic` | Opus 5 | Opus 5 |
  | `openai` | GPT-5.6 Sol | GPT-5.6 Sol |

  `mix-1` / `mix-2` keep **coder ≠ verifier vendor** so the independent check inherits no blind spot;
  `mix-2` is `mix-1` flipped.

## Layout

- `AGENTS.md` — one-line redirect into `.github/copilot-instructions.md`.
- `.github/copilot-instructions.md` — the **canonical** per-session contract: golden rules #0–#11 + working
  agreement + **Project profile** (the SSOT, incl. the Commands table). This README is a non-authoritative overview.
- `.github/agents/` — **one self-contained file per agent**, and all a consumer ever loads: the composed
  assistant `<PERSONA>.md`, plus `anders` / `dave` / `bhaskar` in a 4-pack. No role bodies, no persona
  overlays, no binder — the assistant's governance and voice are composed into its single file at install.
- `.github/agent-templates/` — **source-only** composition inputs, never copied into a consumer:
  `roles/` (`conductor` for the 4-pack, `solo` for the 1-pack) and `personas/` (`jarvis` / `friday`).
- `.github/skills/` — `agentify`, `preflight`, `retrospective` (framework-owned) plus `build-test` +
  `build-test-full`, which are seeded at install from your gate interview and consumer-owned after.
- `docs/` — `design.md`, `meta-design.md`, `backlog.md`, `features/TASK_FILE_TEMPLATE.md`.
- `LICENSE` (MIT) + plumbing (`.editorconfig`, `.gitignore`, `.gitattributes`, `.vscode/`).

