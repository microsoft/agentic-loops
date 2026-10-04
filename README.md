# agentify

A **project-agnostic agent-governance framework for GitHub Copilot**. Its guardrails and skills keep
each agent in a hub-and-spoke loop in its own lane, and the loop ships slices that are easy to review.
It learns from its own work. You can use a hands-free 4-pack (conductor, coder, verifier, and
architect) or a solo generalist.

The human designs the lanes, guardrails, and constraints and always makes the final decision.

You design the loops and guardrails and keep improving them. The agents do the work for you. The
system runs retrospectives and learns from them.

## Packs

`agentify` asks for a pack and a persona at installation. Neither has a default.

| Pack | Makeup | Separation of duties | Tokens | Use when |
|------|--------|----------------------|--------|----------|
| **1-pack** | One generalist designs, implements, verifies, reviews, and owns git. | Waived | Lightest | Small, low-risk work |
| **4-pack** | Conductor + Anders (architect), Dave (coder), Bhaskar (verifier) | Strict | Heavy | Independent review matters |

## Workflows

Choose one at installation. There is no default. Each workflow works with each pack.

| Workflow | Working location | Branch | Record |
|----------|------------------|--------|--------|
| **Worktree** | Separate linked worktree per named item | `wi/<id>` | `work/<id>.md` |
| **Feature** | Feature branch in the chosen checkout | `vibe/<nnn>-<feature_name>` | `docs/features/<nnn>-<feature_name>.md` |

Worktree logs have Definition, Progress, Learnings, Artifacts, and Open sections. Feature records
use the numbered design, slice, and task template. Installation produces only the rules and template
for the selected workflow. Existing branches and work history are kept.

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
gates, and human approvals, but uses its own branch and log.

**① Hands-free loop: WIP mode**

```
  ① HANDS-FREE LOOP · one task at a time

      ┌───────────────────────────────────────────────────┐
      │  Human · decides · E2E-tests · merges · deploys   │
      └───────────────────────────────────────────────────┘
          │ requests              ▲ escalate anytime
          ▼                       │
      ┌───────────────────────────────────────────────────┐
   ┌─►│  Assistant · conductor · owns git + task file     │
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

**② Design session: new-feature mode**

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

**③ Retrospective loop: self-learning**

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

1. Run `.github/skills/agentify.md` from this checkout against a target repository, on a branch that
   is not trunk.
2. Choose a pack, persona, workflow, and form of address.
3. Review Agentify's repository scan: the generated `docs/design.md`, the Commands table taken from
   CI, the gate recipes, the test classification, and the preflight gates. If there is no CI
   evidence, explain how to get or run the required commands.
4. Approve the user-scoped `bro` and `yagni` skills. Preflight refreshes them from this repository.
   The project gets no copy.
5. Say whether the project has a local run and liveness mechanism. A “no” removes those duties.
6. Invoke the installed assistant.

Installation is one-shot. The target owns every installed file. If the installer was staged in the
target, it deletes itself, the source templates, version stamps, update markers, and bootstrap
references after it generates the active governance.

## Writing styles

The governance sets an English style for each reader:

| Writer to reader | Style |
|------------------|-------|
| Assistant to human | The persona sets the interaction style. |
| Agent to agent | ASD-STE100 |
| Governance (`AGENTS.md`, `.github/`, work records and their templates) | ASD-STE100 |
| Everything else (`README.md`, other `docs/` files, code comments, commits, PRs, proposals, and Teams messages) | Plain language with Chicago Manual of Style mechanics |

ASD-STE100 here means its writing rules and its approved vocabulary, plus technical names, technical
verbs, and domain words. No style uses em-dashes. See guardrail 0 in
`.github/copilot-instructions.md`.

## User skills

You can also install these skills without the governance framework:

```powershell
$base = 'https://raw.githubusercontent.com/microsoft/agentic-loops/master/skills'
foreach ($f in 'bro/SKILL.md', 'bro/LICENSE', 'yagni/SKILL.md', 'yagni/LICENSE.agentic-loops') {
  $dest = Join-Path $HOME ".copilot/skills/$f"
  New-Item -ItemType Directory -Force (Split-Path $dest) | Out-Null
  Invoke-WebRequest "$base/$f" -OutFile $dest
}
```

- [`yagni`](skills/yagni/SKILL.md) combines design, code, and writing guidance for text that people
  read. It replaces `simple-docs`.
- [`bro`](skills/bro/SKILL.md) explains the previous reply again in ASD-STE100, with diagrams where
  they help.

Preflight downloads each required skill file over plain HTTPS, with no `gh` and no token. It
replaces the installed copy only if the file is missing or different. A repository update does not
change existing installed copies or projects that were already agentified.

## Model

Every agent uses **GPT-6.1 Sol** (`gpt-6.1-sol`) with high reasoning by default, in both packs.
Agent frontmatter uses `model: GPT-6.1 Sol (copilot)` and `reasoning: high`. Agents can also use
`grok-4.7` with `xhigh` reasoning. Anthropic and other models need the human's explicit permission.

## Task markers

- **`LIM:`** records a limitation as a future todo in `docs/backlog.md`.
- **`TODO:`** records work for the current session in its task list and active work record.

The assistant records tagged items before it continues and keeps their status current. Recording a
limitation does not schedule its implementation. Unfinished session todos are reported at handoff.

## Source layout

- `.github/agent-templates/`: source-only role and persona inputs.
- `.github/agents/`: 4-pack sub-agent sources.
- `.github/skills/agentify.md`: one-shot installer. It is never copied.
- `.github/skills/`: installed Markdown, diagram, preflight, retrospective, and gate recipes.
- `skills/`: installable `bro` and `yagni` sources. They are user-scoped and never copied into
  consumers.
- `.github/instructions/`: path-scoped language rules, including .NET.
- `docs/`: design templates, work method, and feature-record template.
- `work/WORK_ITEM_TEMPLATE.md`: worktree-record template.

## Installed layout

- `.github/copilot-instructions.md`: shared guardrails and commands.
- `.github/agents/`: one complete file for each installed agent.
- `.github/instructions/`: path-scoped language rules.
- `.github/skills/`: project-owned Markdown, diagram, preflight, retrospective, and gate recipes.
- `docs/design.md`: project architecture, operations, and conventions.
- `docs/meta-design.md`: the selected workflow, design method, and test taxonomy.
- `docs/features/` or `work/`: the selected work-record format and its template.
