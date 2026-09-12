# Feature: Task markers and one agent model
**Branch:** vibe/009-agent-task-markers
**Status:** Complete

## Requirements

- Use `gpt-5.6-sol` for all agents.
- Capture human-tagged `LIM:` items as limitations and future todos.
- Capture human-tagged `TODO:` items as work for the current session.
- Leave the paused repository deletion and uncommitted Unicode changes untouched.

## Decision

Keep marker semantics and capture ownership in shared guardrails. Reuse the backlog for future work
and the session task list plus active work record for current tasks. Use the existing work-record
formats rather than introducing another file or skill.

Fix the model in source agent frontmatter and assistant composition. Remove installer model profiles
so neither pack can generate an agent on another model. Keep maximum reasoning.

## Tasks

| # | Task | Status |
|---|------|--------|
| T1 | Set source agents and installer to GPT-5.6 Sol | Complete |
| T2 | Add marker capture to shared rules and both work-record formats | Complete |
| T3 | Review composition, preserve paused changes, and publish a PR | Complete |

## Risks

- Marker capture must not override role boundaries or authorize writing to trunk.
- Without an approved working root, file capture remains pending in session tracking.
- Existing installed agents are not changed by updating framework sources.

## Progress

- 2026-09-12: Created an isolated worktree from `origin/master` at `a299ab7`.
- 2026-09-12: Used the repo's Copilot model spelling, `GPT-5.6 Sol (copilot)`, for the requested
  `gpt-5.6-sol`. Bhaskar already used it; Anders, Dave and installer composition now agree.
- 2026-09-12: Added `LIM:` backlog capture and `TODO:` session capture. Preserved the feature/worktree
  formats and delegated-agent boundaries. The installer preserves an existing backlog.
- 2026-09-12: Reviewed source declarations, assistant composition and both work-record mappings.
  Published `7a859b5` on this branch and opened [PR #14](https://github.com/microsoft/agentic-loops/pull/14).
  The paused Unicode edits remain only in the separate skills-migration worktree.

## Open

- PR review and merge approval remain pending. Repository deletion and installation remain untouched.
