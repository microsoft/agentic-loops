# Feature: Consolidate user skills
**Branch:** vibe/008-skills-migration
**Status:** In Progress

## Requirements

- Move all current skills from the personal skills repository into agentic-loops.
- Preserve the approved YAGNI contents and bro attribution/licenses.
- Update installation, preflight and documentation references.
- Merge the migration before deleting the retired GitHub repository. Keep local backups.
- Do not alter the unrelated .NET and workflow-choice work.

## Design Options (Ox)

### O1 - Root skills directory

- Description: Preserve `skills/bro/` and `skills/yagni/` for normal `gh skill` discovery.
- Pros: Existing install paths work without hidden-directory flags; governance recipes stay separate.
- Cons: Two skill locations, serving different scopes.

**Selected: O1.** The human approved migration, destination merge and then source-repository deletion.

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1 | Skills hosted and consumed from agentic-loops | - |
| S2 | Retired GitHub repository deleted after destination publication | S1 |

## Tasks (Tx)

| # | Slice | Task | Status |
|---|-------|------|--------|
| T1 | S1 | Copy all four skill/license files and update active references | Complete |
| T2 | S1 | Validate, publish and merge the migration | In Progress |
| T3 | S2 | Confirm skills on master, then delete the old GitHub repository | Pending |

## Risks (Rx)

- R1: Deleting the source before publication would break installation. Merge and confirm discovery first.
- R2: Installed metadata may name the retired repository. Preflight installs from the required source
  before invoking update; update comparisons stay in the selected user-skill root.
- R3: Bootstrap cleanup could erase the required URLs now that they name this framework. Exempt only
  user-skill source URLs, not general framework provenance.

## Assumptions (Ax)

- A1: The current skills are `bro` and `yagni` from source commit `a9b49ac`. `simple-docs` is superseded.

## Deferrals (Dx)

- D1: Existing installed copies and previously agentified repositories are not rewritten.
- D2: Feature 006 and PR #11 remain independent and unmerged by this migration.

## Notes & Decisions

- 2026-09-12: The human explicitly approved merging this migration before permanently deleting
  `parthopdas/skills` on GitHub. Local clones and worktrees are retained as backups.
- 2026-09-12: Created an isolated worktree from `origin/master` at `53bcfc1`. Feature IDs 006 and 007
  were already reserved by existing work.
- 2026-09-12: Carried forward the approved skill contents: no Exploration section, design from Anders,
  code from Dave, Clean Architecture where applicable, a meta-design link, and 26 Unicode diagram
  characters plus ASCII fallback. Bro instructions and both license files remain unchanged.
- 2026-09-12: Both skills now use `gh skill`; the former raw-file bro installation path is no longer
  needed. Historical feature 005 records the old hosting and is intentionally unchanged.
- 2026-09-12: All four migrated files match the approved source after newline normalization.
  `gh skill publish --dry-run` accepts both skills. It reports the existing optional YAGNI license-field
  omission and absent tag protection; neither was changed as part of this move.
