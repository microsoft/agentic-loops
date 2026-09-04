# Feature: External skills
**Branch:** vibe/005-external-skills
**Status:** Complete

## Requirements

- Install `bro` and `simple-docs` at user scope.
- Refresh them from GitHub during preflight.
- Never copy them into consumers.
- Publish `simple-docs` in `parthopdas/skills`.

## Design Options

### O1 — User-scoped skills
- Pros: no consumer copy; GitHub remains authoritative.
- Cons: preflight needs network access; updates require restart.

**Decision: O1.**

## Slices

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1 | Publish and wire external skills | - |

## Tasks

| # | Slice | Task | Status |
|---|-------|------|--------|
| T1 | S1 | Publish `simple-docs` | Complete |
| T2 | S1 | Add change-aware preflight refresh | Complete |

## Risks

- R1: Upstream changes load after restart.
- R2: GitHub failure blocks preflight.

## Assumptions

- A1: Copilot CLI and `gh skill` are available.

## Deferrals

- None.
