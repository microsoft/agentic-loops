# Feature: Collapse the agent layout to one self-contained file per agent
**Branch:** vibe/004-collapse-agent-layout
**Status:** Complete

## Requirements

Agentified repos must end up with **no framework ceremony**. `rust-analyzer.vs` was agentified from
this framework, then hand-corrected (`b8ca5b9`, Ruling X) into the shape a consumer should have had
from the start: every agent is one self-contained file in `.github/agents/`, with no role body,
persona overlay, or binder to compose at runtime.

Make `agentify` produce that shape directly, and fold in the governance changes made downstream in
`rust-analyzer.vs` plus the in-flight edits already in this repo's working tree.

Human rulings taken during this feature:

- Composition happens **at install time**, not runtime — the framework keeps DRY sources, the consumer
  gets one file.
- Golden rule #8 (*Writing tests* → `docs/meta-design.md#writing-tests`) is **kept**; the numbering
  stays #0–#11. (`rust-analyzer.vs` dropped it; that was the wrong direction.)
- The gate recipes are **consumer-owned**, and `agentify` must **ask** which gates are relevant and how
  to run each.
- The app run/restart & liveness mechanism gets the same treatment — **ask** whether it is relevant and
  how to do it, rather than shipping a placeholder.
- Model profiles are named `mix-1` | `mix-2` | `anthropic` | `openai` (the README's naming wins over
  the skill's `both`).

## Design Options (Ox)

### O1 — Compose at install
Keep `roles/` + `personas/` as source-only inputs in the framework; `agentify` merges role + persona
into one `.github/agents/<PERSONA>.md`.
- Pros: consumer sees zero indirection; framework stays DRY (one body per role, one tail per persona).
- Cons: install does real work; the composed file must be regenerated, not hand-edited.

### O2 — Pre-composed templates
Ship four ready-made files (`conductor-jarvis`, `conductor-friday`, `solo-jarvis`, `solo-friday`);
install is a dumb copy.
- Pros: no install-time reasoning.
- Cons: each role body duplicated ×2 — guaranteed drift; a role edit must land in two files.

**Recommended: O1 — the consumer-facing result is identical, and the framework avoids duplicated
governance text, which is the thing most likely to rot.**

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1    | Consumers get one self-contained file per agent; framework keeps DRY sources | - |

## Tasks (Tx)

| #  | Slice | Task | Status | Commit |
|----|-------|------|--------|--------|
| T1 | S1    | Relocate role/persona sources to `.github/agent-templates/{roles,personas}/`; make roles self-contained with `{{PERSONA}}`; make personas appendable tails; delete `binder.md` | Complete | - |
| T2 | S1    | Slim `copilot-instructions.md`; move installer prose into `agentify.md`; new Commands floor | Complete | - |
| T3 | S1    | Reduce `AGENTS.md` to a redirect | Complete | - |
| T4 | S1    | Rewrite `agentify.md`: composition algorithm, profile + gate interview, `mix-*` profiles, legacy cleanup | Complete | - |
| T5 | S1    | Reseed both gate recipes as consumer-owned, prune-to-fit | Complete | - |
| T6 | S1    | Add named consumer extension regions to every framework-owned file; repoint `preflight.md` + `retrospective.md` + golden rule #10 | Complete | - |
| T7 | S1    | Update `README.md` layout, getting-started interview, model-profile naming | Complete | - |
| T8 | S1    | Verify: compose all 4 pack×persona combinations; confirm every governance path resolves | Complete | - |
| T9 | S1    | Extension-region + diff-and-confirm update model; split `meta-design.md` taxonomy vs stack mechanism; add the testing interview | Complete | - |

## Notes & Decisions

**Layout change.** `.github/agent-roles/` and `.github/personas/` are gone. Their content now lives
under `.github/agent-templates/` (`roles/`, `personas/`) as **source-only** material that is never
copied into a consumer. `.github/agent-templates/binder.md` is deleted — the binder existed only to
compose at runtime, which is precisely the ceremony being removed.

**Composition contract.** `{{PERSONA}}` is the only substituted token; the leading `<!-- … -->`
provenance comment (and its trailing blank line) is stripped from each source; output is frontmatter +
role body + persona tail + `learnings` region, exactly one blank line between parts. The `rules` region
arrives with the role body. Verified by composing all four pack×persona combinations and asserting: no
residual token, no provenance leak, exactly one `# <PERSONA> etiquette` heading, balanced region
markers, and frontmatter `name` matching the filename stem.

**Ruling — consumer extension regions + diff-and-confirm (human, this session).** Framework-owned
files are refreshed on update, which silently destroyed any governance a consumer wrote into them.
`rust-analyzer.vs` already had **five** such edits (Bhaskar rule #8, Dave rules #10/#11, the JARVIS
Gate-3 bootstrap paragraph, an entire Gate 3 in `preflight.md`, and a "Bootstrap ownership" bullet in
`copilot-instructions.md`) — all of which its first update would have wiped. Every framework-owned file
now ships **named, empty extension regions** (`<!-- AGENTIFY:BEGIN <name> -->` … `<!-- AGENTIFY:END
<name> -->`) at sanctioned points: `rules` in `copilot-instructions.md` and every agent file,
`learnings` in every agent file, `gates` in `preflight.md`, `testing` in `meta-design.md`, `notes` in
`AGENTS.md`. Update harvests regions by name, refreshes, and re-injects. As a backstop, update also
diffs each framework-owned file against `git show <recorded-hash>:<path>` **outside** the regions and
**stops to ask** on any difference. This makes the recorded framework hash load-bearing.

**Ruling — one marker mechanism (human).** The one-sided `AGENTIFY:PRESERVE` tail marker introduced
earlier this session is superseded by paired `BEGIN`/`END` regions, because a tail marker cannot
express a mid-file extension point (RA's Gate 3 sits between Gate 2 and *Pass*). A legacy-marker
conversion step is documented in `agentify.md` → Update.

**Ruling — `meta-design.md` splits taxonomy from mechanism (human).** Golden rule #8 points every agent
in every consumer at `docs/meta-design.md#writing-tests`, but that section hardcoded xUnit traits and
filters while being framework-owned and refreshed — unusable for a Rust/TS/Python adopter and
unfixable by them. The framework now owns only the boundary taxonomy (unit / integration / acceptance);
the stack's marking and selection mechanism is asked for by the install interview and written into the
`testing` region.

**Ruling — "Preflight checks" terminology (human).** The concept stays, but is renamed **mechanical
policy checks** in `meta-design.md` and explicitly distinguished from the loop gates in
`.github/skills/preflight.md`. The interview asks each consumer which ones it automates and which
Commands row runs each.

**Ruling — `AGENTS.md` stays a bare redirect (human).** It carries a `notes` extension region so a
consumer using non-Copilot tooling can add to it without fighting refresh.

**Defect found and fixed en route.** `retrospective.md` instructed the coder to write per-agent
learnings into the agent files, but every agent file is framework-owned and refreshed on update — so
those learnings were silently destroyed by the next `agentify` run. Fixed by the extension-region
model above; golden rule #10 and `retrospective.md` both now point at the `learnings` / `rules`
regions.

**Commands table.** The required floor shrank to `build`, `test:quick`, `test:full`. `lint`,
`format:fix`, and `format:check` became optional and ship as `none`, because a stack can legitimately
enforce them another way — `rust-analyzer.vs` does exactly this, its Release build failing on any
analyzer or StyleCop diagnostic. Requiring a separate lint pass there forced a fake value.

**Kept against the downstream precedent.** `rust-analyzer.vs` dropped the *Framework version adopted*
and *Model profile* profile fields as having no live reference. They do have one: `agentify`'s Update
step reads both to re-stamp agent models and to know what version it is upgrading from. Dropping them
makes a consumer un-updatable, so they stay.

## Deferrals (Dx)

- D1: This repo does not dogfood the composed assistant — `.github/agents/` here holds only the
  sub-agents, and `agent-templates/` holds the sources. Shipping a composed `JARVIS.md` in this repo
  would reintroduce the duplication O2 was rejected for. Revisit only if running the loop *inside*
  agentify becomes necessary.
- D2: `docs/features/001-*.md` and `003-*.md` still describe the old binder/role/persona layout. They
  are historical records of completed features and are intentionally left unedited.
