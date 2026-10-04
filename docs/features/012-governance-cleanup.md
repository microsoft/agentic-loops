# Feature: Governance cleanup
**Branch:** vibe/012-governance-cleanup
**Status:** Complete

## Requirements

- Use "assistant" as the only name for the loop agent role. Remove "conductor".
- Fix these findings from the review of the human's governance changes:
  - The model and built-in agent rules exist only in the 4-pack role template.
  - The `yagni` link to the meta-design does not resolve after installation.
  - The new execution safety text in the 4-pack role template is not ASD-STE100+.
  - The general English rules are under the reader-style list.
  - The `bro` description and rule 4 have text errors.
  - `README.md` repeats guardrail 0.

## Design Options (Ox)

### O1: Direct text fixes
- Description: Change only the lines that have the problem. Rename `roles/conductor.md` to
  `roles/team.md`, to pair with `roles/solo.md`.
- Pros: Small change.
- Cons: None.

**Recommended: O1, because the fixes are text only.**

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1    | One name for the assistant, and the review findings are fixed | - |

## Tasks (Tx)

| #  | Slice | Task | Status  | Commit |
|----|-------|------|---------|--------|
| T1 | S1    | Rename `roles/conductor.md` to `roles/team.md`. Replace "conductor" with "assistant" | Done | - |
| T2 | S1    | Add execution safety to `roles/solo.md`. Point `agentify` to the role template | Done | - |
| T3 | S1    | Restore the full meta-design URL in `yagni` | Done | - |
| T4 | S1    | Write the 4-pack execution safety in ASD-STE100+ | Done | - |
| T5 | S1    | Put the general English rules under "In all English" | Done | - |
| T6 | S1    | Fix the `bro` description and rule 4 | Done | - |
| T7 | S1    | Link `README.md` to guardrail 0, and do not repeat it | Done | - |

## Risks (Rx)

- R1: Projects that were already agentified keep the old role text until they update.

## Assumptions (Ax)

- A1: Feature records 001 and 003 keep "orchestrator" and "conductor", because they are history.

## Deferrals (Dx)

- D1: This repository has no assistant agent file, so the model and built-in agent rules do not
  apply to its own assistant.
- D2: The bare URLs in the guardrail note are not changed.

## Notes & Decisions

- The human removed the `bro` and `yagni` license files and the `bro` source line.
