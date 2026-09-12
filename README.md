# agentify

A **project-agnostic agent-governance framework for GitHub Copilot**: guardrails and skills that keep
a hub-and-spoke loop in-lane, shipping reviewable slices. Self-learning, with a hands-free 4-pack —
conductor, coder, verifier, architect — or a solo generalist.

The human designs the lanes, guardrails, and constraints and stays final decision-maker.

You design and continuously enhance the loops and guardrails; the agents do the work for you. The
system retrospects and self-learns.

## Packs

`agentify` asks for a pack and persona at install; neither has a default.

| Pack | Makeup | Separation of duties | Tokens | Use when |
|------|--------|----------------------|--------|----------|
| **1-pack** | One generalist designs, implements, verifies, reviews, and owns git. | Waived | Lightest | Small, low-risk work |
| **4-pack** | Conductor + Anders (architect), Dave (coder), Bhaskar (verifier) | Strict | Heavy | Independent review matters |

## Workflows

Choose one at installation; there is no default. Either supports either pack.

| Workflow | Working location | Branch | Record |
|----------|------------------|--------|--------|
| **Worktree** | Separate linked worktree per named item | `wi/<id>` | `work/<id>.md` |
| **Feature** | Feature branch in the chosen checkout | `vibe/<nnn>-<feature_name>` | `docs/features/<nnn>-<feature_name>.md` |

Worktree logs have Definition, Progress, Learnings, Artifacts and Open sections. Feature records
use the numbered design/slice/task template. Installation produces only the selected workflow's
rules and template; existing branches and work history are preserved.

```text
+----------------+ worktree  +------------------------+
| Install choice | --------> | Linked worktree + log  |
+----------------+           +------------------------+
        | feature
+------------------------+
| Feature branch + record|
+------------------------+
Boxes = installed workflows; arrows = the human's choice.
```

## The loops

The examples below show the feature workflow. Worktree mode keeps the same pack roles, review
gates and human approvals, using its own branch and log instead.

**① Hands-free loop — WIP mode**

```
  ① HANDS-FREE LOOP · one task at a time

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
   └──┤ commit vibe/<nnn> · push · PR                     │
      └───────────────────────────────────────────────────┘
   ↺ next task

  slice end → pause only if sign-off needed · never trunk · never deploy
```

**② Design session — new-feature mode**

```
  ② DESIGN SESSION · Anders leads with the human

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

  Feature ─► Slices ─► Tasks ─► loop ①
```

**③ Retrospective loop — self-learning**

```
  ③ RETROSPECTIVE LOOP · every ≥ 5 features

   ┌───────────┐    ┌──────────┐    ┌────────┐    ┌────────┐
   │ Assistant │───►│  Anders  │───►│ Human  │───►│  Dave  │
   │  reminds  │    │ distills │    │ okays  │    │applies │
   └───────────┘    └──────────┘    └────────┘    └────────┘
        ▲                                                │
        └───────────── updated guardrails ↺ ─────────────┘
          (1-pack: the solo assistant fills all roles)
```

## Install

1. Run `.github/skills/agentify.md` from this checkout against a target repository on a non-trunk
   branch.
2. Choose a pack, persona, workflow, and form of address. Choose a model profile or accept `mix-1`.
3. Review Agentify's repository scan: generated `docs/design.md`, CI-derived Commands table, gate
   recipes, test classification, and preflight gates. If CI evidence is absent, supply how to obtain
   or run the required commands.
4. Approve the user-scoped `bro` and `yagni` skills. Preflight refreshes them from this repository; the
   project receives no copy.
5. Answer whether the project has local run/liveness. A “no” removes those duties.
6. Invoke the installed assistant.

Installation is one-shot. The target owns every installed file. If temporarily staged in the target,
the installer deletes itself, source templates, version stamps, update markers, and bootstrap
references after generating the active governance.

## User skills

These can also be installed without the governance framework:

```powershell
gh skill install microsoft/agentic-loops yagni --agent github-copilot --scope user
gh skill install microsoft/agentic-loops bro --agent github-copilot --scope user
```

- [`yagni`](skills/yagni/SKILL.md) combines design, code and writing guidance, with a bounded Unicode
  diagram palette. It replaces `simple-docs`.
- [`bro`](skills/bro/SKILL.md) re-explains the previous reply in plain language. Original skill by
  Hermes Agent + Luka, from [luchasarie/bro-skill](https://github.com/luchasarie/bro-skill), with its
  [MIT license](skills/bro/LICENSE) preserved.

Preflight migrates required skills with missing or different source metadata before checking updates.
Existing installed copies and previously agentified projects are not changed by a repository update.

## Model profiles

Every agent uses maximum reasoning.

| Profile | Designs + codes | Verifies + drives |
|---------|-----------------|-------------------|
| `mix-1` *(default)* | Claude Opus 5 | GPT-5.6 Sol |
| `mix-2` | GPT-5.6 Sol | Claude Opus 5 |
| `anthropic` | Claude Opus 5 | Claude Opus 5 |
| `openai` | GPT-5.6 Sol | GPT-5.6 Sol |

## Source layout

- `.github/agent-templates/` — source-only role and persona inputs.
- `.github/agents/` — 4-pack sub-agent sources.
- `.github/skills/agentify.md` — one-shot installer; never copied.
- `.github/skills/` — installed Markdown, preflight, retrospective, and gate recipes.
- `skills/` — installable `bro` and `yagni` sources; user-scoped, never copied into consumers.
- `.github/instructions/` — path-scoped language rules, including .NET.
- `docs/` — design templates, work method and feature-record template.
- `work/WORK_ITEM_TEMPLATE.md` — worktree-record template.

## Installed layout

- `.github/copilot-instructions.md` — shared guardrails and commands.
- `.github/agents/` — one complete file per installed agent.
- `.github/instructions/` — path-scoped language rules.
- `.github/skills/` — project-owned Markdown, preflight, retrospective, and gate recipes.
- `docs/design.md` — project architecture, operations, and conventions.
- `docs/meta-design.md` — the selected workflow, design method and test taxonomy.
- `docs/features/` or `work/` — the selected work-record format and its template.
