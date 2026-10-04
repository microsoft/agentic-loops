# Feature: Review fixes for writing styles
**Branch:** vibe/011-review-fixes
**Status:** Complete

## Requirements

Fix these findings from the review of commit `b1a78f9`:

- `bro` must not use tools, but its diagram rules are only a link. Permit `bro` to read that link.
- The rule "Assistant to the human: highly informal" conflicts with the formal JARVIS persona. The
  persona sets the interaction style.
- `README.md` said that `bro` writes in plain language with Chicago mechanics. `bro` writes in
  ASD-STE100.

## Design Options (Ox)

### O1: Direct text fixes
- Description: Change only the lines that have the problem.
- Pros: Small change.
- Cons: None.

**Recommended: O1, because the fixes are text only.**

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1    | The three findings are fixed | - |

## Tasks (Tx)

| #  | Slice | Task | Status  | Commit |
|----|-------|------|---------|--------|
| T1 | S1    | Permit `bro` to read the diagram rules link | Done | - |
| T2 | S1    | The persona sets the assistant-to-human style, in guardrail 0 and `README.md` | Done | - |
| T3 | S1    | Correct the `bro` output style in `README.md` | Done | - |

## Risks (Rx)

- R1: None.

## Assumptions (Ax)

- A1: None.

## Deferrals (Dx)

- D1: The human did not schedule the other review findings: the 1-pack delegation exception, the
  preflight installed-location check, and three rewording changes (Bhaskar determinism, .NET access
  modifier, backlog order).

## Notes & Decisions
