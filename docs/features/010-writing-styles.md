# Feature: Writing styles
**Branch:** vibe/010-writing-styles
**Status:** Complete

## Requirements

- R1: Set an English style for each writer and reader:
  - Assistant to human: highly informal. The persona sets the voice.
  - Agent to agent: ASD-STE100.
  - Agent to writing (Markdown, design docs, proposals, Teams messages, skills, and all other
    English): ASD-STE100 or plain language with Chicago Manual of Style mechanics.
- R2: Write all in-scope governance, docs, and skills again in their style. Do not lose accuracy.
  Ask the human before you delete content.
- R3: Add formatting rules, link rules, and a Teams chats section to `yagni`.
- R4: Preflight Gate 3 installs the required user skills with a plain HTTP download from GitHub. It
  does not use `gh skill`.
- R5: The human makes the changes with the assistant directly. The agentic loop does not run. Do not
  commit before the human reviews.

Out of scope (human decision): `LICENSE` files, `SECURITY.md`, completed records
`docs/features/001` to `009`, and configuration files (`.editorconfig`, `.gitignore`, `.vscode/`).

## Design Options (Ox)

### O1: One style for each reader
- Description: ASD-STE100 for agent-to-agent text and governance. Plain language with Chicago
  mechanics for all other English.
- Pros: Agents get short, unambiguous instructions. Humans get readable docs.
- Cons: Two written styles to maintain.

### O2: Plain language for all written English
- Description: One style for governance and docs.
- Pros: One style.
- Cons: Governance instructions are less strict and can be read in more than one way.

**Recommended: O1, because governance is instructions for agents, and STE writing rules make
instructions unambiguous.**

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1    | Style rules, rewritten files, new Gate 3, and `yagni` additions | - |

## Tasks (Tx)

| #  | Slice | Task | Status  | Commit |
|----|-------|------|---------|--------|
| T1 | S1    | Add the style-per-reader rules to guardrail 0 and `README.md` | Done | - |
| T2 | S1    | Replace Gate 3 with an HTTPS download. Update the `README.md` install steps | Done | - |
| T3 | S1    | Add formatting, links, and Teams chats rules to `yagni` | Done | - |
| T4 | S1    | Write governance, agent templates, and skills again in ASD-STE100 | Done | - |
| T5 | S1    | Write `README.md` and `docs/` again in plain language with Chicago mechanics | Done | - |
| T6 | S1    | Compare each file with HEAD for lost names, links, tokens, and markers | Done | - |
| T7 | S1    | Human review | Done | - |
| T8 | S1    | TODO: Remove all em-dashes from governance, including frontmatter descriptions. Add an explicit no-em-dash rule to guardrail 0 | Done | - |
| T9 | S1    | TODO: Keep the assistant instructions as they are at HEAD | Dropped by the human | - |
| T10 | S1   | TODO: State the plain language with Chicago mechanics rule in `yagni`. Agent-to-agent rules stay only in governance | Done | - |
| T11 | S1   | TODO: Move the diagram rules to `.github/skills/diagram.md`. Link to it from everywhere else. Change `bro` output to ASD-STE100 with diagrams | Done | - |
| T12 | S1   | TODO: Remove the skills folder from the scratch copy. Remove it from `skillDirectories`. Skills come only from this repo's origin. The human chose to keep the folder, because it is a tracked clone | Done | - |
| T13 | S1   | Set the default model to `gpt-6.1-sol` with high reasoning. Permit `grok-4.7` with `xhigh` reasoning. Other models need explicit permission | Done | - |
| T14 | S1   | The assistant gives tasks to team members. Other agents need explicit permission | Done | - |
| T15 | S1   | ASD-STE100 uses its approved vocabulary, plus technical names, technical verbs and domain words | Done | - |

## Risks (Rx)

- R1: A rewrite can change the meaning of a rule. Mitigation: T6 and the human review.
- R2: The raw GitHub URL uses `master`. If the default branch changes, Gate 3 fails and blocks.
- R3: `yagni` and `bro` link to `diagram.md` on `master`. The link fails until this branch merges.

## Assumptions (Ax)

- A1: ASD-STE100 means its writing rules and its approved vocabulary. Exceptions: technical names,
  technical verbs and domain words.
- A2: Text that is emitted as it is (diagrams, banners, persona sample lines, agentify role
  descriptions, frontmatter descriptions, and sentinels) does not change.
- A3: `raw.githubusercontent.com` does not need authentication for this public repository.

## Deferrals (Dx)

- D1: None.

## Notes & Decisions

- Guardrail 0 also has the formatting and link rules of `yagni`, so that governance does not depend
  on a user skill.
- `anders.md` referred to "Designing a feature". The correct section is "Designing work". Fixed.
- The JARVIS persona is "extremely polite and formal". The assistant-to-human rule says "highly
  informal". The human must decide which rule has priority.
- In `docs/design.md`, "Use OpenTelemetry only for telemetry" has two possible meanings. The text
  did not change.
