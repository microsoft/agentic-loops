# Feature: Collapse the agent layout
**Branch:** vibe/004-collapse-agent-layout
**Status:** Complete

## Requirements

- Install one self-contained file per agent.
- Keep role and persona composition source-only.
- After install, delete the installer, source templates, version data, markers, provenance, and
  bootstrap references from consumers.
- Keep project details out of generic governance.
- Isolate C# rules in a path-scoped Markdown file.
- Ask whether liveness applies; remove all related target instructions when it does not.
- Generate `docs/design.md` from a repository scan for human review.
- Infer commands and gates from CI; ask when evidence is absent.
- Install the generic Markdown skill.
- Record the human's preferred form of address in `docs/design.md`.

## Decisions

- Installation is one-shot. Its final step removes all bootstrap traces; consumers own the result.
- Existing consumers are maintained directly; there is no framework update protocol.
- Project architecture and operations live in `docs/design.md`.
- Commands remain in `.github/copilot-instructions.md`.
- Test taxonomy is generic; projects fill their own marking and selection mechanism.
- Only the assistant spawns agents. Web-backed agents run serially.
- C# rules live in `.github/instructions/csharp.instructions.md`.
- Liveness instructions are source-optional and never survive a declined install answer.
- Project-specific agent constraints live in `docs/design.md`.

## Design options

### O1 — Compose at install

Keep role and persona templates here; install one merged assistant file.

- Pros: consumer has no indirection; source stays DRY.
- Cons: installer performs composition.

### O2 — Pre-compose every combination

- Pros: simple copy.
- Cons: duplicated governance drifts.

**Decision: O1.**

## Tasks

| # | Task | Status |
|---|------|--------|
| T1 | Move source-only role/persona inputs under `.github/agent-templates/` | Complete |
| T2 | Compose one assistant file at install | Complete |
| T3 | Reduce `AGENTS.md` to a redirect | Complete |
| T4 | Make gates and testing stack-specific | Complete |
| T5 | Add pack, persona, and model selection | Complete |
| T6 | Backport generic execution safety | Complete |
| T7 | Remove update/version/marker machinery | Complete |
| T8 | Move project facts to `docs/design.md` | Complete |
| T9 | Isolate C# guidance | Complete |
| T10 | Verify all pack/persona outputs are unbranded | Complete |
| T11 | Make liveness governance opt-in | Complete |
| T12 | Generate design and gates from repository evidence | Complete |
| T13 | Restore downstream-generic wording and Markdown guidance | Complete |

## Notes

The source repository retains its installer and templates. Consumers receive only active governance.
Visual Studio, VSIX, Rust nightly, and product compatibility rules remain in their owning project.
