# Feature: Consolidate user skills
**Branch:** vibe/008-skills-migration
**Status:** In Progress

## Requirements

- Move all current skills from the personal skills repository into agentic-loops.
- Preserve the approved YAGNI contents and bro attribution/licenses.
- Use common modern Unicode for diagrams rather than a handpicked 26-character palette.
- Update installation, preflight and documentation references.
- Merge the migration before deleting the retired GitHub repository. Keep local backups.
- Preserve and integrate the separately approved .NET and workflow-choice work.

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
| T2 | S1 | Validate, publish and merge the migration | Complete |
| T3 | S2 | Confirm skills on master, then delete the old GitHub repository | Paused |
| T4 | S1 | Apply the broader Unicode policy requested after migration | Complete |

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
- 2026-09-12: Opened [PR #12](https://github.com/microsoft/agentic-loops/pull/12). The human then expanded
  approval to publish and merge all agentify changes, including the pre-existing .NET work.
- 2026-09-12: Workflow-choice PR #11 and .NET PR #13 are merged. Integrated both into this branch;
  resolved the installer choice-number overlap and README layout overlap by retaining the workflow
  choice, bro/yagni sources, .NET rules and work-record templates together.
- 2026-09-12: PR #12 merged at `a299ab7`. Both skills are discoverable from master, with their license
  files. Master preflight no longer uses the retired skill sources.
- 2026-09-12: Source deletion returned HTTP 403 because the CLI token lacked `delete_repo`. The local
  backup was refreshed and passed `git fsck`. The human approved reauthorization, which is awaiting
  GitHub approval; the source repository has not yet been deleted.
- 2026-09-12: The human rejected the overly narrow 26-character restriction. Replaced it with full
  standard Box Drawing and Block Elements ranges, common arrows and technical symbols. Excluded
  esoteric/custom-font glyphs; an unknown font alone no longer triggers ASCII fallback.
- 2026-09-12: The first authorization attempt was stopped. A second attempt completed successfully;
  authorization is no longer blocked, but repository deletion remains paused.
- 2026-09-12: The human approved publishing and merging all remaining agentify changes. Resumed the
  Unicode update without resuming repository deletion. The model and task-marker changes in PR #14
  are now merged.
