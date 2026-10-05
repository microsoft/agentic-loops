---
name: agentify
description: One-shot install of the governance framework into a target repository.
---

Run this skill against a target on a branch that is not trunk. You can invoke the skill from this
checkout, or you can put it in the target temporarily.

The installation is one-shot. The target owns each installed file. Never copy these items into the
target: this skill, the source templates, framework version data, update markers, or source
provenance. The only exception to the provenance rule is the required user-skill source URLs in
preflight.

## Discover the project

Examine the repository before you ask questions:

1. Read the existing architecture, contribution, operations, and build documentation.
2. Make a list of the source roots, entry points, manifests, project files, modules, dependencies,
   tests, generated/acquired artifacts, ignore rules, deployment files, and compatibility
   declarations.
3. Trace the main dependency direction and the runtime flows. Describe only the current behavior. Put
   planned work in `docs/backlog.md`.
4. Find the project constraints that agents must obey: supported platforms/versions, artifacts,
   bootstrap ownership, verification evidence, release boundaries, and language rules. Put these in
   `docs/design.md`, not in the generic agent files.
5. Write a draft of `docs/design.md` from the evidence. Include repository operations, the system
   overview, architecture, key components, dependency direction, build/verification, cross-cutting
   concerns, and conventions. Keep its global constraints. Cite repository paths. Mark each
   uncertainty. Never invent details.
6. Write drafts of the applicable path-scoped language files in `.github/instructions/`. Keep only
   the rules that repository evidence supports or that the human approves. Remove languages and rules
   that do not apply.

If `docs/design.md` exists, do not replace it. Propose a minimal update that evidence supports.

### Discover CI and commands

Before you ask for commands, search the CI/CD and task artifacts. These include:

- `.github/workflows/`, Azure Pipelines YAML, `.gitlab-ci.yml`, `Jenkinsfile`, CircleCI, Buildkite,
  and other pipeline definitions.
- `Makefile`, `Taskfile`, `Justfile`, package-manager scripts, solution/project files, and build/test
  scripts.
- Formatter, linter, analyzer, code-generation, dependency, mutation, coverage, and acceptance-test
  configuration.

Trace the scripts that the pipeline invokes. Do not copy only the outer pipeline step. For each
command that you infer, record its source file and job/step. Derive these items:

- `build`, `test:quick`, and `test:full`.
- The optional Commands rows.
- The membership and order of the fast and full gates.
- The setup or environment checks that belong in `.github/skills/preflight.md`.
- The test marking/filtering for `docs/meta-design.md`.

Never copy secrets or CI-only environment assumptions. Make sure that each referenced script or file
exists in the target. Prefer one repository script that CI and the local gates both use.

Show these items together for human review: the design draft, the inferred Commands table, the gate
recipes, and the preflight gates. Apply the corrections before you write them. If there are no CI
artifacts, or if you cannot derive a required command, ask the human how to get or run it. Do not
invent a command. Do not refer to a missing script. If the human asks for wrapper scripts, first
create them as target-owned project files.

## Confirm choices

Ask for these items:

1. **Pack**: `1-pack`, `4-pack`, or `5-pack`. There is no default. A 5-pack is a 4-pack plus Kittu
   (tracker). The 4-pack and the 5-pack are the team packs.
2. **Persona**: one name from `.github/agent-templates/personas/`. There is no default.
3. **Workflow**: `worktree` or `feature`. There is no default. This choice selects both the branch
   isolation and the work-record format, not only the location of the edits. Use the table below to
   explain the choice.
4. **Address**: how the agents address the human. There is no default. Record it in
   `docs/design.md`.
5. **Discovery review**: approval of, or corrections to, the design, commands, gates, and testing
   mechanism.
6. **Liveness**: ask if the project has a local run/restart and liveness mechanism. If the answer is
   no, ask nothing more about it.
7. **User skills**: approval to install and refresh the required user-scoped `bro` and `yagni`
   skills from GitHub during preflight. If the human does not approve, stop the installation.

| Workflow | Where work happens | Branch | Work record |
|----------|--------------------|--------|-------------|
| `worktree` | A separate linked worktree per named item | `wi/<id>` | `work/<id>.md` |
| `feature` | A feature branch in the chosen checkout | `vibe/<nnn>-<feature_name>` | `docs/features/<nnn>-<feature_name>.md` |

Each workflow supports each pack. The human selects the workflow at installation, not for each task.
Reject a persona with the name `anders`, `dave`, `bhaskar`, or `kittu`.

## Install

1. If destination governance that is not bootstrap governance exists, stop. Ask before you replace or
   merge it.
2. Copy `AGENTS.md`, `.github/copilot-instructions.md`, the applicable `.github/instructions/`,
   `.github/skills/markdown.md`, `.github/skills/diagram.md`, `.github/skills/preflight.md`,
   `.github/skills/retrospective.md`, `.github/skills/build-test.md`,
   `.github/skills/build-test-full.md`, and `docs/meta-design.md`.
3. Write the approved design draft to `docs/design.md`. Copy the `docs/backlog.md` template only if
   the target has no backlog. Never replace existing items.
4. Copy `.editorconfig`, `.gitignore`, `.gitattributes`, and `.vscode/` only if they are not there.
5. Compose one assistant file as "Compose the assistant" specifies.
6. For a `4-pack`, also copy `anders.md`, `dave.md`, and `bhaskar.md`. For a `5-pack`, also copy
   `kittu.md`. For a `1-pack`, copy none of them.
7. Put `model: GPT-6.1 Sol (copilot)` and `reasoning: high` on each installed agent.
8. Configure the selected workflow as "Configure the workflow" specifies. Write the approved commands
   into the Commands table. Write the testing details into `docs/meta-design.md`. Write the gate
   order and details into the two build-test recipes. Write the startup gates into `preflight.md`.
   Write the approved language rules into `.github/instructions/`.
9. Keep `bro` and `yagni` at user scope. Never copy the source `skills/` directory into the target.
10. Process each `OPTIONAL:LIVENESS` block:
   - **Yes:** remove the marker lines, keep the instructions, and record the mechanism in
     `docs/design.md`.
   - **No:** remove each full block. No liveness instruction can remain.
11. Delete the optional command rows and recipe steps that are not used.
12. Remove all bootstrap traces from the target.
13. Do the final checks.

Do not copy `.github/skills/agentify.md`, `.github/agent-templates/`, README files, or feature
history.

## Cleanup

After you generate the target governance:

1. Keep the project-authored content in legacy marker regions. Then remove the marker lines.
2. Delete these items from the target if they are there: `.github/skills/agentify.md`,
   `.github/agent-templates/`, `.github/agent-roles/`, and `.github/personas/`.
3. Move the live project facts and commands out of each legacy Project profile. Apply the
   pack/persona/workflow choices and the fixed model to the agent layout and frontmatter. Then delete
   the obsolete profile and the version field.
4. Move the project-specific agent rules into `docs/design.md`. Keep their meaning.
5. Remove the bootstrap references from the installed governance. Keep the required user-skill source
   URLs.
6. Show the cleanup diff before you finish. Never delete project-authored content.

First, resolve the source and target roots. Clean only the resolved target paths. Never change the
source checkout, unless it is explicitly the target that you convert.

## Configure the workflow

Apply the selected column to all installed governance. This includes the composed assistant and the
team-pack agents. Do not edit the source files. Do not replace text in existing project work records.

| Token | `worktree` | `feature` |
|-------|------------|-----------|
| `{{WORK_BRANCH}}` | `wi/<id>` | `vibe/<nnn>-<feature_name>` |
| `{{WORK_RECORD}}` | `work/<id>.md` | `docs/features/<nnn>-<feature_name>.md` |
| `{{WORK_TEMPLATE}}` | `work/WORK_ITEM_TEMPLATE.md` | `docs/features/TASK_FILE_TEMPLATE.md` |

1. Copy the selected template from its source path to the same target path.
2. In `docs/meta-design.md`, keep the body of the selected `WORKFLOW:WORKTREE` or `WORKFLOW:FEATURE`
   block. Delete the other block. Remove the marker lines of the two blocks.
3. After you compose the assistant, replace each token above in the installed files.
4. Record the chosen workflow, branch pattern, record path, and working-root convention in
   `docs/design.md`. For `worktree`, use an existing approved worktree helper, or use native
   `git worktree` commands. Do not invent a helper. Do not refer to a missing helper.
5. Keep the existing branches, worktrees, and historical records. If active work uses a different
   convention, ask how to manage that transition. Do not rename or migrate it silently.

The generated files contain only the chosen workflow. There is no runtime workflow loader, no second
work-record format, and no copied installer. The two workflows keep the same pack boundaries,
build/test gates, human approvals, and one-PR-per-record rule.

## Compose the assistant

For a team pack, use `roles/team.md`. For a `1-pack`, use `roles/solo.md`. Append the selected
persona file.

1. Remove the leading `<!-- ... -->` provenance block of each source file and the blank line after
   it.
2. Replace each `{{PERSONA}}` in the role with the persona name in upper case.
3. Process each `OPTIONAL:KITTU` block: for a `5-pack`, remove only the marker lines; for a `4-pack`,
   remove each full block.
4. Write this frontmatter:

       ---
       name: <PERSONA>
       description: <role description>
       model: GPT-6.1 Sol (copilot)
       reasoning: high
       ---

5. Append the role and the persona, with one blank line between the parts.
6. Write `.github/agents/<PERSONA>.md`.

Role descriptions:

- `4-pack`: `Runs the agentic loop (hub-and-spoke). Coordinates Dave, Bhaskar, and Anders. Read-only inspection + git/task-file management only; never designs, codes, or verifies.`
- `5-pack`: `Runs the agentic loop (hub-and-spoke). Coordinates Dave, Bhaskar, Anders, and Kittu. Read-only inspection + git/task-file management only; never designs, codes, or verifies.`
- `1-pack`: `Solo generalist for the 1-pack: designs, implements, verifies, and reviews in one context; owns git + the task file. Never deploys.`

## Model

All roles in all packs use `gpt-6.1-sol` by default, with the name `GPT-6.1 Sol (copilot)` in the
agent frontmatter, and `reasoning: high`. There is no model-profile choice. "Execution safety" in
the role template gives the other permitted models.

## Final checks

- No required placeholder remains.
- The assistant contains no `{{PERSONA}}`, no provenance comment, and no duplicate etiquette
  heading.
- No `OPTIONAL:LIVENESS` or `OPTIONAL:KITTU` marker remains. If the human declined liveness, no
  related instruction remains. A `4-pack` contains no Kittu instruction.
- No `{{WORK_BRANCH}}`, `{{WORK_RECORD}}`, `{{WORK_TEMPLATE}}`, or `WORKFLOW:` marker remains.
- Guardrail #3, the installed assistant, and each architect, preflight, meta-design, and
  retrospective file agree on the selected branch and work record. The template that was not
  selected is not installed.
- For `worktree`, creation, resumption, and all handoffs use the selected linked working root. No
  rule switches the main checkout to an item branch. For `feature`, the numbered records and the
  branch creation in the chosen checkout stay correct.
- Each command and referenced script exists or resolves in the target.
- The human selected the workflow. The human approved the generated design, Commands table, recipes,
  and preflight gates.
- Only the expected agents exist.
- Each installed agent uses `model: GPT-6.1 Sol (copilot)` and `reasoning: high`.
- The user task-marker rules stay after installation: `LIM:` uses the backlog, and `TODO:` uses
  session tracking and the selected work record. The existing backlog items stay correct.
- The installed governance contains no framework version, no update marker, and no installer skill.
  The framework name can occur only in the required user-skill source URLs.
- The required user skills exist only at user scope.
- All Markdown links resolve from their installed locations.
- `.github/skills/preflight.md` passes.

Maintain existing adopters directly in their repositories. This installer does not update them.
