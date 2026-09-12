---
name: retrospective
description: Every five completed work records, distill durable lessons into minimal governance changes.
---

A minimal, count-based review that turns delivery experience into durable guardrails.

## Packs

In a 4-pack, the assistant reminds, Anders distills, and Dave applies. In a 1-pack, the assistant does
all three. The human approves every guardrail change.

## When

After completing a work record, count records marked `**Status:** Complete` in the same directory as
`{{WORK_TEMPLATE}}`, excluding the template. Compare with `completed=N` on the last Log line; start
at zero if there is no entry. Remind the human when the count grows by five or more.

Do not count directories, templates or unfinished records as completed work.

## Sources

Review work records, especially post-review and post-test-fix notes; `docs/design.md`;
`docs/backlog.md`; agent and skill files; and commits since the last Log entry. Verify lessons against
repository evidence.

## Produce

- **All-agent guardrails:** candidate additions or refinements for `.github/copilot-instructions.md`.
- **Project facts:** architecture, compatibility, operations, and role constraints for
  `docs/design.md`.
- **Per-agent learnings:** short, durable notes for the relevant agent file.

Skip item-specific details. Prioritize recurring, high-signal lessons.

## Apply

1. Anders proposes exact, minimal redlines.
2. The human approves guardrail changes.
3. Dave writes approved guardrails, project facts, and per-agent learnings to their listed
   destinations, then fixes stale references.
4. Keep guardrail numbers stable and cite them by number.

Do not overdo it.

## Log

    - YYYY-MM-DD | completed=N | <one-line summary>
