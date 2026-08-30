---
name: agentify
description: One-shot install of the governance framework into a target repository.
---

Run against a target on a non-trunk branch. The skill may be invoked from this checkout or staged
temporarily in the target.

Installation is one-shot. The target owns every installed file. Never copy this skill, source
templates, framework version data, update markers, or source provenance into the target.

## Discover the project

Inspect before asking questions:

1. Read existing architecture, contribution, operations, and build documentation.
2. Inventory source roots, entry points, manifests, project files, modules, dependencies, tests,
   generated/acquired artifacts, ignore rules, deployment files, and compatibility declarations.
3. Trace the main dependency direction and runtime flows. Describe current behavior only; put planned
   work in `docs/backlog.md`.
4. Identify project constraints agents must honor: supported platforms/versions, artifacts, bootstrap
   ownership, verification evidence, release boundaries, and language rules. Put these in
   `docs/design.md`, not generic agent files.
5. Draft `docs/design.md` from evidence. Include repository operations, system overview, architecture,
   key components, dependency direction, build/verification, cross-cutting concerns, and conventions.
   Cite repository paths. Mark uncertainties; never invent details.
6. Draft applicable path-scoped language files under `.github/instructions/`. Keep only rules supported
   by repository evidence or approved by the human; remove irrelevant languages and rules.

If `docs/design.md` exists, propose a minimal evidence-backed update instead of replacing it.

### Discover CI and commands

Search CI/CD and task artifacts before asking for commands, including:

- `.github/workflows/`, Azure Pipelines YAML, `.gitlab-ci.yml`, `Jenkinsfile`, CircleCI, Buildkite,
  and other pipeline definitions;
- `Makefile`, `Taskfile`, `Justfile`, package-manager scripts, solution/project files, and build/test
  scripts;
- formatter, linter, analyzer, code-generation, dependency, mutation, coverage, and acceptance-test
  configuration.

Trace invoked scripts rather than copying only the outer pipeline step. For each inferred command,
record its source file and job/step. Derive:

- `build`, `test:quick`, and `test:full`;
- optional Commands rows;
- fast/full gate membership and order;
- setup or environment checks that belong in `.github/skills/preflight.md`;
- test marking/filtering for `docs/meta-design.md`.

Never copy secrets or CI-only environment assumptions. Confirm every referenced script/file exists in
the target. Prefer one repository script shared by CI and local gates.

Present the design draft, inferred Commands table, gate recipes, and preflight gates together for
human review. Apply corrections before writing them. If no CI artifacts exist, or any required command
cannot be derived, ask the human how to obtain or run it. Do not invent a command or reference a
missing script. If the human requests wrapper scripts, create them as target-owned project files first.

## Confirm choices

Ask for:

1. **Pack** — `1-pack` or `4-pack`; no default.
2. **Persona** — one name from `.github/agent-templates/personas/`; no default.
3. **Model profile** — `mix-1` (default), `mix-2`, `anthropic`, or `openai`.
4. **Address** — how agents should address the human; no default. Record it in `docs/design.md`.
5. **Discovery review** — approval or corrections for the design, commands, gates, and testing
   mechanism.
6. **Liveness** — ask whether the project has a local run/restart and liveness mechanism. If no, ask
   nothing further about it.

Reject a persona named `anders`, `dave`, or `bhaskar`.

## Install

1. Stop if non-bootstrap destination governance already exists; ask before replacing or merging it.
2. Copy `AGENTS.md`, `.github/copilot-instructions.md`, applicable `.github/instructions/`,
   `.github/skills/markdown.md`, `.github/skills/preflight.md`, `.github/skills/retrospective.md`,
   `.github/skills/build-test.md`, `.github/skills/build-test-full.md`, `docs/meta-design.md`, and
   `docs/features/TASK_FILE_TEMPLATE.md`.
3. Write the approved design draft to `docs/design.md`. Create `docs/backlog.md` only when absent.
4. Copy `.editorconfig`, `.gitignore`, `.gitattributes`, and `.vscode/` only when absent.
5. Compose one assistant file as described below.
6. For a `4-pack`, also copy `anders.md`, `dave.md`, and `bhaskar.md`. For a `1-pack`, copy none.
7. Stamp each installed agent's model and `reasoning: max`.
8. Write approved commands into the Commands table, testing details into `docs/meta-design.md`, gate
   order/details into both build-test recipes, startup gates into `preflight.md`, and approved
   language rules into `.github/instructions/`.
9. Process every `OPTIONAL:LIVENESS` block:
   - **Yes:** remove marker lines, keep the instructions, and record the mechanism in `docs/design.md`.
   - **No:** remove each whole block. No liveness instruction may remain.
10. Delete unused optional command rows and recipe steps.
11. Remove every bootstrap trace from the target.
12. Run the final checks.

Do not copy `.github/skills/agentify.md`, `.github/agent-templates/`, README files, or feature history.

## Cleanup

After generating the target governance:

1. Preserve any project-authored content inside legacy marker regions, then remove the marker lines.
2. Delete the target's `.github/skills/agentify.md`, `.github/agent-templates/`,
   `.github/agent-roles/`, and `.github/personas/` if present.
3. Migrate live project facts and commands out of any legacy Project profile, apply pack/persona/model
   choices to the agent layout and frontmatter, then delete the obsolete profile and version field.
4. Move project-specific agent rules into `docs/design.md`, preserving their meaning.
5. Remove bootstrap references from installed governance.
6. Show the cleanup diff before finishing. Never delete project-authored content.

Resolve source and target roots first. Clean only resolved target paths; never alter the source
checkout unless it is explicitly the target being converted.

## Compose the assistant

Use `roles/conductor.md` for a `4-pack`, otherwise `roles/solo.md`. Append the selected persona file.

1. Remove each source file's leading `<!-- ... -->` provenance block and following blank line.
2. Replace every `{{PERSONA}}` in the role with the upper-case persona.
3. Emit this frontmatter:

       ---
       name: <PERSONA>
       description: <role description>
       model: <profile model>
       reasoning: max
       ---

4. Append the role and persona with one blank line between parts.
5. Write `.github/agents/<PERSONA>.md`.

Role descriptions:

- `4-pack`: `Runs the agentic loop (hub-and-spoke). Coordinates Dave, Bhaskar, and Anders. Read-only inspection + git/task-file management only; never designs, codes, or verifies.`
- `1-pack`: `Solo generalist for the 1-pack: designs, implements, verifies, and reviews in one context; owns git + the task file. Never deploys.`

## Model profiles

| Role | `mix-1` | `mix-2` | `anthropic` | `openai` |
|------|---------|---------|-------------|----------|
| Architect | Claude Opus 5 | GPT-5.6 Sol | Claude Opus 5 | GPT-5.6 Sol |
| Coder | Claude Opus 5 | GPT-5.6 Sol | Claude Opus 5 | GPT-5.6 Sol |
| Verifier | GPT-5.6 Sol | Claude Opus 5 | Claude Opus 5 | GPT-5.6 Sol |
| Assistant | GPT-5.6 Sol | Claude Opus 5 | Claude Opus 5 | GPT-5.6 Sol |

Use the Copilot model names `Claude Opus 5 (copilot)` and `GPT-5.6 Sol (copilot)`.

## Final checks

- No required placeholder remains.
- The assistant contains no `{{PERSONA}}`, provenance comment, or duplicate etiquette heading.
- No `OPTIONAL:LIVENESS` marker remains. If liveness was declined, no related instruction remains.
- Every command and referenced script exists or resolves in the target.
- The human approved the generated design, Commands table, recipes, and preflight gates.
- Only the expected agents exist.
- Installed governance contains no framework name, version, update marker, or installer skill.
- All Markdown links resolve from their installed locations.
- `.github/skills/preflight.md` passes.

Existing adopters are maintained directly in their repositories; this installer does not update them.
