---
name: retrospective
description: Every five completed work records, distill durable lessons into minimal governance changes.
---

A minimal review, based on a count, that changes delivery experience into durable guardrails.

## Packs

In a team pack (4-pack or 5-pack), the assistant reminds, Anders distills, and Dave applies. In a
1-pack, the assistant does all three. The human approves each guardrail change.

## When

After you complete a work record, count the records that have `**Status:** Complete`. Count only in
the directory of `{{WORK_TEMPLATE}}`, and do not count the template. Compare the count with
`completed=N` on the last Log line. If there is no entry, start at zero. When the count increases by
five or more, remind the human.

Do not count directories, templates, or unfinished records as completed work.

## Sources

Review these sources: work records (especially the post-review and post-test-fix notes),
`docs/design.md`, `docs/backlog.md`, agent and skill files, and the commits since the last Log entry.
Verify each lesson against repository evidence.

## Produce

- **All-agent guardrails:** proposed additions or refinements for
  `.github/copilot-instructions.md`.
- **Project facts:** architecture, compatibility, operations, and role constraints for
  `docs/design.md`.
- **Per-agent learnings:** short, durable notes for the applicable agent file.

Do not include details that apply to only one item. Give priority to lessons that recur and have
high value.

## Apply

1. Anders proposes exact, minimal redlines.
2. The human approves the guardrail changes.
3. Dave writes the approved guardrails, project facts, and per-agent learnings to their destinations
   in the list above. Then Dave fixes stale references.
4. Keep the guardrail numbers stable, and cite each guardrail by its number.

Do not do more than necessary.

## Log

    - YYYY-MM-DD | completed=N | <one-line summary>
