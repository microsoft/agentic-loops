# Feature: Kittu and the 5-pack
**Branch:** vibe/013-kittu-5-pack
**Status:** Complete

## Requirements

- Add Kittu (tracker) as a framework role. Kittu tracks CI and PR gates, validates pushed revisions,
  and does maintenance and follow-up tasks. Kittu never edits tracked files.
- Add a 5-pack: the 4-pack plus Kittu.
- Make no other change to this repository.

## Design Options (Ox)

### O1: Optional Kittu blocks in the team role template
- Description: Add `OPTIONAL:KITTU` blocks to `roles/team.md`. `agentify` keeps them for a 5-pack
  and removes them for a 4-pack.
- Pros: One team template for the two team packs.
- Cons: One more marker type.

**Recommended: O1, because it follows the `OPTIONAL:LIVENESS` pattern.**

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1    | `agentify` can install a 5-pack with Kittu | - |

## Tasks (Tx)

| #  | Slice | Task | Status  | Commit |
|----|-------|------|---------|--------|
| T1 | S1    | Add `.github/agents/kittu.md` | Done | - |
| T2 | S1    | Add `OPTIONAL:KITTU` blocks to `roles/team.md` | Done | - |
| T3 | S1    | Add the 5-pack to `agentify`, preflight, retrospective, and `roles/solo.md` | Done | - |
| T4 | S1    | Add the 5-pack to `README.md` | Done | - |

## Risks (Rx)

- R1: Existing adopters do not get Kittu until they update.

## Assumptions (Ax)

- A1: Kittu uses the same default model as the other roles.

## Deferrals (Dx)

- D1: The loop diagrams in `README.md` do not show Kittu.

## Notes & Decisions

- The human authorized only the Kittu change in this repository.
