---
name: retrospective
description: Periodic cross-feature governance retrospective. Every five completed features, distill durable lessons into minimal governance changes.
---

A minimal, count-based review that turns delivery experience into durable guardrails.

## Packs

In a 4-pack, the assistant reminds, Anders distills, and Dave applies. In a 1-pack, the assistant does
all three. The human approves every guardrail change.

## When

After each feature, compare the feature count with the `features=N` value on the last Log line. Remind
the human when it grows by five or more.

    (Get-ChildItem docs/features/*.md | ? { $_.Name -ne 'TASK_FILE_TEMPLATE.md' }).Count

## Sources

Review feature files, especially post-review and post-test-fix notes; `docs/design.md`;
`docs/backlog.md`; agent and skill files; and commits since the last Log entry. Verify lessons against
repository evidence.

## Produce

- **All-agent guardrails:** candidate additions or refinements for `.github/copilot-instructions.md`.
- **Project facts:** architecture, compatibility, operations, and role constraints for
  `docs/design.md`.
- **Per-agent learnings:** short, durable notes for the relevant agent file.

Skip feature-specific details. Prioritize recurring, high-signal lessons.

## Apply

1. Anders proposes exact, minimal redlines.
2. The human approves guardrail changes.
3. Dave writes approved guardrails, project facts, and per-agent learnings to their listed
   destinations, then fixes stale references.
4. Keep guardrail numbers stable and cite them by number.

Do not overdo it.

## Log

    - YYYY-MM-DD · features=N · <one-line summary>

- 2026-08-03 · features=0 · Retrospective process created.
