# Feature: Install-time workflow choice

**Branch:** vibe/007-workflow-choice
**Status:** Complete

## Requirements

- Ask for a full `worktree` or `feature` workflow at installation, with no default.
- Worktree: linked worktree, `wi/<id>`, `work/<id>.md`.
- Feature: branch in the chosen checkout, `vibe/<nnn>-<feature_name>`,
  `docs/features/<nnn>-<feature_name>.md`.
- Support both packs without changing their role boundaries or gates.
- Preserve existing branches, records and unrelated local .NET changes.

## Design Options (Ox)

### O1 - Isolation choice only

Keep numbered features in both modes; vary only the working directory.
Simpler record handling, but does not provide the requested work-log workflow.

### O2 - Complete workflow choice

Select the branch, working-root rules, record format and template together.
More installation surfaces, but consistent with the two requested workflows.

**Selected: O2**, approved by the human.

Use the existing install-time composition approach: resolve three work-path tokens and retain one
meta-design workflow block. Consumers receive plain instructions, not a runtime profile loader.

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1 | Either complete workflow can be installed for either pack | - |

## Tasks (Tx)

| # | Slice | Task | Status | Commit |
|---|-------|------|--------|--------|
| T1 | S1 | Add installer choice and selected record template | Complete | - |
| T2 | S1 | Wire both roles, architect, guardrails, preflight and retrospective | Complete | - |
| T3 | S1 | Review composed consumer outputs and documentation | Complete | - |

## Risks (Rx)

- R1: Mixed branch/log rules after installation. Resolve tokens across all installed governance
  and remove the unselected workflow block and template.
- R2: Disturbing existing work. Preserve branches, logs and worktrees; ask before transitions.

## Assumptions (Ax)

- A1: Worktree IDs are kebab-case and do not use feature numbering.
- A2: Workflow selection changes organization, not pack responsibilities or validation gates.

## Deferrals (Dx)

- D1: Switching workflows in existing adopters requires a separate approved migration.
- D2: The local .NET governance changes remain on their original branch, unmodified.

## Notes & Decisions

- 2026-09-12: Human selected O2 and approved a separate upstream worktree.
- 2026-09-12: Reserved feature 007 because feature 006 exists in the original checkout's
  uncommitted work. This branch starts at the shared committed base `53bcfc1`.
- 2026-09-12: Added both install choices, the work-item log template, working-root rules and
  completed-record retrospective counting. No installer was run against another repository.
- 2026-09-12: Reviewed 16 workflow/pack/persona/liveness compositions. Each retained only its
  chosen branch, record and template; assistant and architect routing agreed with preflight.
  Original .NET edits remained unchanged. No consumer was migrated.
