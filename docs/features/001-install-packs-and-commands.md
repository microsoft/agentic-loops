# Feature: Install packs (1-pack / 4-pack) + command abstraction + README compaction
**Branch:** vibe/001-install-packs-and-commands
**Status:** In Progress

## Requirements

Human-provided (this session):

1. README: add a **Getting Started** section that tells the user to run the `agentify` skill.
2. **Install options** — a **1-pack** (lightweight) and a **4-pack** (full team); many projects don't
   need the token-heavy 4-agent loop. Put a **section per pack** in the README (and the agentify skill).
3. **Abstract named commands** obtained from the consumer: `build`, `lint`, `format:fix` + `format:check`
   (kept as two variants — fix for Dave's fast gate, check for Bhaskar's full gate), `test:quick`,
   `test:full`; plus **optional constraints**: DRY checker, mutation tester, CRAP metric (and the
   ref-repo-validated additions: type-check, restore, coverage, IaC lint/validate, security scan).
4. README is **too verbose** → compact it (golden rule #0).
5. The `agentify` skill must **ask the user which pack** (no silent default) and **ask an opt-in
   question** for the optional DRY/mutation/CRAP constraints.
6. **User-visible note** (README + agentify): stronger and more varied constraints ⇒ agents are more
   easily supervised.
7. **Fix the loop instructions**: the loop works **one task at a time** (not one slice); makes
   **reasonable assumptions during each task**; **pauses at the end of a slice** only if human
   intervention is required and/or slice assumptions need validation.

Decisions locked with the human:

- 1-pack = a **distinct generalist agent FRIDAY** (Anders' O1); the four canonical agents are untouched.
- Preflight identity gate is **pack-symmetric**: 4-pack ⇒ loop is JARVIS-only; 1-pack ⇒ loop is
  FRIDAY-only (error/hand back if the invoking agent isn't the pack's driver).
- Core commands (build/lint/format/test) are filled in **both** packs; only the heavy optional gates
  are opt-in. agentify **asks** the pack (no default). Compact the ~70-line ASCII diagram.

## Design Options (Ox)

Per Anders' analysis (the 1-pack semantics):

### O1 — distinct generalist (FRIDAY); 4-pack untouched  ✅ CHOSEN
Keeps all four canonical invariants literally true (JARVIS stays "never codes"); clean mental model
(1-pack = one worker + human; 4-pack = adds orchestration + strict lanes); minimal blast radius. Cost:
one merged governance file (irreducible — a 1-pack needs every role in one place).

### O2 — single `jarvis.md` with a profile-switched "solo mode"
Rejected: makes the framework's crispest invariant ("JARVIS never codes / the loop is JARVIS-only")
conditional; identity bleed for 4-pack readers.

### O3 — JARVIS "solo edition" (divergent same-named file per pack)
Rejected: two same-named divergent files = an update/versioning trap for the `agentify` skill.

**Recommended & chosen: O1 (FRIDAY).**

## Slices (Sx)

| Slice | Outcome | Depends on |
|-------|---------|------------|
| S1 | Command abstraction: Commands table (SSOT) in the Project profile; the two gate-skills become name-referencing recipes | — |
| S2 | Packs: FRIDAY generalist, Pack profile field, pack-symmetric preflight, loop-instruction fix, pack-aware `agentify` install | S1 |
| S3 | README compaction: Getting Started + per-pack Install options + compact model + trims + supervision note | S1, S2 |

## Tasks (Tx)

| #  | Slice | Task | Status | Commit |
|----|-------|------|--------|--------|
| T1  | S1 | Commands table in the Project profile: required core rows (`build`, `lint`, `format:fix`, `format:check`, `test:quick`, `test:full`) + optional rows (`dry-check`, `mutation-test`, `crap-check`) each with `or "none"`; note "add any other commands your stack needs"; per-row gate tag (fast/full); bare-`FILL_ME` prose discipline | Pending (rev1) | - |
| T2  | S1 | `build-test.md` → fast recipe referencing command names in order (format:fix→lint→test:quick→build), skip `none`; keep fast/Dave/no-integration/zero-warning semantics | Pending (rev1) | - |
| T3  | S1 | `build-test-full.md` → full recipe (format:check→lint→build→test:full→dry-check?→mutation-test?→crap-check?), skip `none`; keep full/Bhaskar semantics | Pending (rev1) | - |
| T4  | S1 | Extend the preflight maintainer bare-`FILL_ME` note to the Commands table; confirm the required-files scan is still valid (no logic change) | Pending | - |
| T5  | S2 | Add a **Pack** field (1-pack \| 4-pack) to the Project profile near the version hash; stamped by agentify (asked, no default) | Pending | - |
| T6  | S2 | Author `.github/agents/friday.md`: merged generalist (Anders+Dave+Bhaskar + JARVIS git/task-file duties); all golden rules **except #2 (waived, explicit)**; never-trunk/vibe/never-deploy/secrets/preflight; single-context phased checklist implementing the corrected loop; self-verifies via the Commands table. Short | Pending | - |
| T7  | S2 | Make `preflight.md` pack-aware with a **symmetric identity gate**: 4-pack ⇒ JARVIS-only; 1-pack ⇒ FRIDAY-only (error/hand back if the invoking agent mismatches). Gate 2 unchanged; Pack must be set | Pending | - |
| T8  | S2 | Fix the loop instructions in `jarvis.md`: one task at a time; reasonable assumptions per task; pause at the **slice boundary** if human intervention/slice-assumption validation is needed | Pending | - |
| T9  | S2 | Update `agentify.md`: **ask the pack (no default)**; 1-pack vs 4-pack copy sets (per-pack sections); stamp Pack; **opt-in question** for optional DRY/mutation/CRAP constraints; move `build-test*.md` to framework-owned; migration note (inline→profile); update the PowerShell sketch; **supervision note** | Pending | - |
| T10 | S3 | README **Getting Started**: run `agentify` → choose pack → fill placeholders (profile incl. Commands, `design.md`) → invoke agent(s) | Pending | - |
| T11 | S3 | README **Install options** with a **section per pack** (1-pack, 4-pack): what ships, the separation-of-duties tradeoff, token rationale, when to pick | Pending | - |
| T12 | S3 | Compact the model: replace the ~70-line ASCII with a ~12-line flow; fix "implement one slice" → "one task"; encode the corrected loop (task-at-a-time, slice-boundary pause) | Pending | - |
| T13 | S3 | Trim & merge: terse Layout (absorb Contents); shrink intro/For-consumer-agents/Versioning/Retrospective; fold the standalone TODO into a one-line pointer to optional Commands; add a brief Commands pointer; add the user-visible **supervision note** | Pending | - |
| T14 | S1 | **Trim the command set to the human's explicit list** everywhere it appears (Commands table, `build-test.md`, `build-test-full.md`, README Commands section): keep only `build`, `lint`, `format:fix`, `format:check`, `test:quick`, `test:full` + optional `dry-check`/`mutation-test`/`crap-check`. **Remove** `restore`, `type-check`, `coverage`, `iac-lint`, `iac-validate`, `security-scan`. Add "consumers add any other commands they need" | Pending | - |
| T15 | S2 | Add a **FRIDAY persona & etiquette** section to `friday.md` (compressed, no info loss): full form *Female Replacement Intelligent Digital Assistant Youth*; successor to J.A.R.V.I.S.; Kerry Condon Irish voice; addresses the human as the project form (she calls Stark "boss"); traits — witty/sassy (Irish slang), deeply empathetic/loyal, hyper-alert/grounded; a curated set of sample lines | Pending | - |
| T16 | S2 | Add the **JARVIS acronym** to `jarvis.md`: *Just A Rather Very Intelligent System* | Pending | - |
| T17 | S2 | Add a **colored ASCII starting banner** to both `jarvis.md` and `friday.md` (printed at session start): pyfiglet `standard` font of the name + acronym tagline; JARVIS in cyan (`\e[96m`), FRIDAY in amber (`\e[38;5;214m`), reset `\e[0m` | Pending | - |
| T18 | S2 | Make the `copilot-instructions.md` **Working agreement** bullets pack-aware: the loop is JARVIS-only (4-pack) / FRIDAY-only (1-pack), and the pack's driver runs preflight (fixes the inconsistency Dave flagged) | Pending | - |

## Risks (Rx)

- R1: 1-pack sacrifices separation of duties (the core value prop). Mitigate: Install-options states the
  tradeoff plainly; agentify **asks** the pack so it's a conscious choice.
- R2: command indirection — a mis-named or `none` command silently skips. Mitigate: explicit ordered
  recipes; the zero-warning gate still fails loudly on a wrong/missing command.
- R3: preflight pack mis-detection if Pack is unset. Mitigate: Pack is stamped by agentify and required;
  unset ⇒ stop.
- R4: compaction drops content some rely on (the big diagram). Mitigate: authoritative detail lives in
  the agent/skill files; the README is explicitly non-authoritative.
- R5: update migration — consumers who filled the old inline `build-test*.md` get framework-owned
  recipes on update. Mitigate: T9 migration note (move commands into the profile first).

## Assumptions (Ax)

- A1: Core commands are filled in both packs; "4-pack-only" applies only to the heavy optional gates.
  (Human-confirmed.)
- A2: Generalist name = FRIDAY. (Human-confirmed.)
- A3: agentify asks the pack; no silent default. (Human-confirmed.)
- A4: Multi-stack repos scope command names by convention (e.g. `web:build`) or a multi-line cell — no
  rigid schema.
- A5: This repo remains a 4-pack user of its own framework and also hosts `friday.md` as a distributable
  artifact.
- A6: agentify itself has no build/test (docs-only); verification = doc consistency + preflight-scan
  validity.

## Deferrals (Dx)

- D1: A 2-pack (JARVIS + one doer) preserving JARVIS purity while cutting tokens.
- D2: Pack switching (1↔4) beyond a re-install.
- D3: Wiring the optional gates into the loop as first-class, per-gate pass/fail steps (richer enforcement).
- D4: Hosting `friday.md` as a dist/template path instead of `.github/agents/`.
- D5: A formal multi-stack command schema (convention suffices).

## Notes & Decisions

- Supervision principle (make user-visible in README + agentify): stronger and more varied constraints
  ⇒ agents are more easily supervised.
- The loop-cadence correction is authoritative in `jarvis.md`; `friday.md` and the README mirror it.

### Revision 1 (mid-loop human input)

- **Trim the command set** to the human's explicit list — the ref-repo-derived extras (`restore`,
  `type-check`, `coverage`, `iac-lint`, `iac-validate`, `security-scan`) were rejected as bloat.
  Retain only `build`, `lint`, `format:fix`/`format:check`, `test:quick`, `test:full` + optional
  DRY/mutation/CRAP; consumers add anything else (T14).
- **FRIDAY persona & etiquette** (compressed, no info loss) + acronym; **JARVIS acronym**; **colored
  ASCII starting banners** for both, shown at session start (T15–T17).
- **Working-agreement pack-awareness** fix (T18).
- **Commit gate (session-scoped):** for THIS session the human asked JARVIS to hold commits — run
  Dave → Bhaskar → Anders, then **wait for explicit go-ahead before committing**. This is a one-off
  session directive, **not** a change to `jarvis.md`'s per-task commit cadence (that governance is
  unchanged).

### Verification — rev1 FAIL → rev2 fixes (Bhaskar)

Bhaskar verified pass1+rev1 → **FAIL** (7 defects + 2 advisories). rev2 resolves:

- **F1 (High)** `agentify` Update must **splice new profile fields** (Pack, Commands table) into existing
  adopters — not keep the profile "verbatim"; migration note reordered (add table → move commands → refresh).
- **F2 (High)** `agentify` now **prompts for the 6 required command values**; optional opt-in "no" leaves
  rows `none`; the 3 optional rows now **ship as `none`** (no sentinel) so they never block preflight.
- **F3 (High)** Install PowerShell sketch **validates the pack** and errors on unset/invalid (no silent 4-pack default).
- **F4 (Med)** `retrospective` made **pack-aware** (FRIDAY performs all roles in a 1-pack; human still approves); README line too.
- **F5 (Med)** README loop cadence: preflight runs **once at session/loop start** (not per task); **blockers escalate immediately**, not only at slice boundaries.
- **F6 (Med)** commit-hold clarified as a **session-scoped** directive (above), not a `jarvis.md` change.
- **F7 (Med)** FRIDAY new-feature order: **cut branch first, then write** the feature file.
- **A1** `jarvis.md` prose literal sentinel → bare `FILL_ME`. **A2** strip banner trailing whitespace (`git diff --check` clean).

### Design review — Anders: APPROVE-WITH-NITS → rev3 nits

Anders reviewed pass1+rev2 at the design level → **APPROVE-WITH-NITS** (O1/FRIDAY + Commands-SSOT + loop cadence sound). rev3 applies his 4 MED nits + doc clarifications:

- **N1** fast gate reordered to `format:fix → lint → build → test:quick` (build before tests, matching the full gate); both recipes note they are authoritative for gate membership+order (reorder per stack, e.g. type-aware linters).
- **N2** recipes skip `none` only for **optional** rows; a **required** command valued `none`/empty is a loud misconfiguration stop (not a silent skip). Commands-table note states required rows must be real commands.
- **N3** FRIDAY uses the **fast** gate (auto-formats via `format:fix`) while implementing and the **full** gate for final self-verify — mirroring Dave→Bhaskar in one agent; `format:fix` is no longer dead in a 1-pack.
- **N4** FRIDAY gains the **retrospective-reminder** duty (≥ ~5 features) that JARVIS already has.
- **N5** doc clarity: recipe is authoritative for order; consumer-added Commands rows run **after** the core steps.

**Human-decision flags (golden rule #6) — carried to the commit gate:**
- **Flag A (back-compat):** this feature changes the filled Project-profile schema (adds `Pack` + Commands table; moves recipes to framework-owned). The `agentify` Update splice (F1) mitigates, but if any **real adopters already exist**, the splice/migration must be human-validated. If zero adopters → greenfield, no issue.
- **Flag B (governance posture):** the 1-pack waives separation of duties (the framework's headline value). Already the locked O1 decision; docs state the tradeoff; human owns "when a 1-pack is appropriate" at install time. No change unless a stronger in-file caveat is wanted.

### Deferral (from Anders N5)

Consumer-added Commands rows execute **after** the core steps in each recipe. A stack needing a **pre-build** step (e.g. `restore`, `type-check`) in the right position must reorder its own (framework-owned) recipe — the recipes are authoritative for order. Deferred; documented in the recipes.

### Revision 4 — persona extraction + consumer choice (user directive, at commit-gate hold)

New directives (post-rev3):
- **Extract flavor.** Pull each agent's **personality, sample dialogs, and etiquette** (plus the acronym
  full-forms and the colored ASCII **banners**) out of the core agent files into a **separate persona
  file/layer**, so the core `.github/agents/*.md` are pure governance/role and read cleanly without it.
  (Inventory: only `jarvis.md` and `friday.md` carry persona/etiquette/banners today; anders/dave/bhaskar
  are already pure-role.)
- **Consumer choice in `agentify` (locked: model B).** Personas are **mandatory — never "off"**; `agentify`
  forces an **explicit, no-default** persona choice. The choice is a **decoupled menu** (model B): the
  consumer picks a persona for their **driver** from the personas in **`.github/personas/`** (today JARVIS,
  FRIDAY), **independent of pack** — so a persona may not match the pack's natural driver name (e.g. a
  1-pack solo driver skinned as "JARVIS"); the human accepted this. Recorded as a Project-profile field
  (a literal value, **no `FILL_ME` sentinel**, **not** a preflight blocker). Persona applies to the
  **driver only**; anders/dave/bhaskar stay pure-role.
- **Persona location (locked).** All persona content lives in **`.github/personas/`** (per-agent overlay
  files, no frontmatter so they aren't registered as invokable agents).
- **Role vs persona (design implication).** Because persona is now decoupled from role, the driver's
  **role governance must be persona-agnostic** and the chosen persona supplies identity/tone/banner — Anders
  to design the composition + invocation-naming (his S4 rev targets model B, not the earlier on/off).
- **README.** Update correspondingly (persona is an optional overlay; how the `agentify` choice works).
- **Flag B (resolved: YES).** Add a stronger in-file caveat on the 1-pack separation-of-duties waiver —
  lives in **core `friday.md`** (governance, not flavor).
- **Flag A (resolved: NONE).** No real adopters yet → greenfield; the `agentify` Update splice needs no
  adopter-migration validation.
- **Commit hold still in force** (session-scoped) — do not commit.

Flow: Anders (design) → Dave (implement, incl. README) → Bhaskar (verify) → hold at commit gate.

**Forks resolved (human):** FORK-1 = **full identity** (the chosen persona sets the driver's Copilot
invocation `name` + banner + voice; a name no longer signals the pack — the `description` does, and
preflight enforces). FORK-2 = **B-β** — role templates in a new **`.github/agent-roles/`**
(`orchestrator.md`, `solo.md`; no frontmatter) + persona overlays in **`.github/personas/`**
(`jarvis.md`, `friday.md`; no frontmatter) + a thin **`.github/agents/driver.md`** binder (frontmatter
`name` = chosen persona; body binds the pack's role + the chosen persona). The old `agents/jarvis.md` /
`agents/friday.md` are removed (their role bodies → `agent-roles/`, their flavor → `personas/`);
anders/dave/bhaskar are untouched. Tasks T19+ to be finalized by Anders for B-β.

**Anders' finalized B-β design (S4 — definitive, no open decisions).**

Source layout after S4 (this repo = the "menu", dogfoods as 4-pack × JARVIS):
- `.github/agent-roles/` (NEW, no frontmatter): `orchestrator.md` (4-pack role, from jarvis.md governance),
  `solo.md` (1-pack role, from friday.md governance + Flag-B caveat).
- `.github/personas/` (NEW, no frontmatter): `jarvis.md` (cyan banner + J.A.R.V.I.S. acronym + etiquette +
  samples), `friday.md` (amber banner + F.R.I.D.A.Y. acronym + traits + samples).
- `.github/agents/`: `driver.md` (NEW thin binder, frontmatter `name`=Persona / `description`=role / `model`;
  **generic** canonical body that resolves role from Pack + persona from Persona at runtime), `anders.md`,
  `dave.md`, `bhaskar.md` (untouched). `jarvis.md` + `friday.md` **deleted**.
- Binder is **generic-resolve-from-profile** (one canonical framework-owned body; agentify only stamps
  frontmatter `name`/`description` + profile `Pack`/`Persona`). Load order: reload copilot-instructions +
  design → read Pack+Persona → role=`4-pack⇒orchestrator|1-pack⇒solo` → load `agent-roles/<role>.md` +
  `personas/<persona>.md` → **print banner first** → preflight → run loop in persona voice. **Conflict rule:
  role governance overrides persona.**

Definitive tasks (each with a Bhaskar-verifiable check):
- **T19** — Create `agent-roles/orchestrator.md` (no frontmatter): migrate jarvis.md governance (drop
  banner/acronym/etiquette/frontmatter); persona-agnostic identity opener; keep loop/modes/boundaries/
  retro-duty; drop banner-print + name-based gate. *Check:* no frontmatter, no `<<FILL_ME:`, zero
  JARVIS/banner/etiquette tokens, loop steps intact.
- **T20** — Create `agent-roles/solo.md` (no frontmatter): migrate friday.md governance; persona-agnostic
  opener; keep #2-waiver + solo loop + self-review + standing duties; **add Flag-B caveat (4 sentences)**;
  drop banner-print + name gate. *Check:* no frontmatter/sentinel, no FRIDAY/banner tokens, `#2…WAIVED`
  present, Flag-B caveat present, solo loop intact.
- **T21** — Create `personas/jarvis.md` (no frontmatter): header note + cyan `\e[96m` banner + J.A.R.V.I.S.
  acronym + `# JARVIS etiquette` + samples. *Check:* no frontmatter, banner+acronym+etiquette+samples,
  no loop/gate/mode governance, diff-check clean.
- **T22** — Create `personas/friday.md` (no frontmatter): header note + amber `\e[38;5;214m`/`\e[93m` banner
  + F.R.I.D.A.Y. acronym + traits (Kerry Condon/"boss") + samples. *Check:* no frontmatter, banner+acronym+
  traits+samples, no governance, diff-check clean.
- **T23** — Create `agents/driver.md` (frontmatter `name: JARVIS` + orchestrator-desc + `model`; canonical
  generic body resolving role/persona from profile; banner-first; preflight; conflict rule). *Check:*
  frontmatter has name+description+model; body has no hardcoded role/persona in its load logic; no
  `<<FILL_ME:`; load-order + conflict rule present.
- **T24** — Delete `agents/jarvis.md` + `agents/friday.md`. *Check:* both absent; no live refs outside
  `docs/features/*`.
- **T25** — Add `Persona:` to Project profile after `Pack:` — literal `JARVIS`, no sentinel, marked
  no-default/not-a-blocker/agentify-stamped. *Check:* `Persona:` line present, value `JARVIS`, no
  `<<FILL_ME:`, adjacent to `Pack:`.
- **T26** — Make `copilot-instructions.md` driver-relative: replace the two Working-agreement bullets
  (driver = `agents/driver.md`; role by Pack; sub-agents refuse+hand back); Path-convention example →
  `agents/driver.md`; golden-rule numbering unchanged. *Check:* zero JARVIS/FRIDAY in file; driver-relative
  bullets present; example updated; rules still #0–#10.
- **T27** — Rework preflight **Gate 1** to driver-only/role-based (driver = `agents/driver.md`, name-agnostic;
  Pack unset ⇒ stop; sub-agent invoker ⇒ refuse+hand back; Persona unset ⇒ non-blocking); reword frontmatter
  +intro; **Gate 2 + 4-file scan unchanged**; `agent-roles/`+`personas/` unscanned. *Check:* Gate 1 keys on
  role not persona name; Gate-2 scan byte-identical; no new scanned dirs.
- **T28** — Update `agentify.md` for B-β: ask+validate persona (menu=`personas/*`, no default); ownership adds
  `agent-roles/`+`personas/`+binder body; copy sets = binder + one role + one persona (+3 sub-agents for
  4-pack); stamp `Persona` + binder `name`/`description`; new PowerShell sketch. *Check:* no "copy jarvis/
  friday/four-canonical" text; persona ask+validate present; sketch copies exactly one role + one persona +
  stamps.
- **T29** — Update `README.md` driver-relative: Getting-Started persona step + invoke-the-driver; Install
  persona/decoupling note; driver-relative model diagram + prose; Layout adds `agent-roles/`+`personas/`;
  update `AGENTS.md` pointers. *Check:* README ≤ ~5.6 KB; no JARVIS-only/FRIDAY-only/merged-into-FRIDAY;
  personas/agent-roles/driver.md mentioned; AGENTS.md lists both new dirs.
- **T30** — Update `retrospective.md` driver-relative: frontmatter/When/Packs → "the driver"; Produce(B) →
  sub-agents + driver **role body**; Sources add `agent-roles/*`+`personas/*`. *Check:* no hardcoded JARVIS/
  FRIDAY reminder; Produce(B) names driver role body; Sources include both dirs.
- **T31** — Repo self-consistency (dogfood — **install-shape axis only: structure + stamps + Gate-1**):
  confirm this repo is a valid **4-pack × JARVIS** install *shape* that passes **Gate-1**. This repo is the
  distributable **menu/template** and **intentionally retains its consumer FILL_ME placeholders, so it does
  NOT — and must not — pass Gate-2** (Gate-2 is a *consumer-completion* gate, not an install-shape check).
  *Check:* **(structure)** `agent-roles/{orchestrator,solo}` + `personas/{jarvis,friday}` present; `agents/
  {driver,anders,dave,bhaskar}` present, `agents/{jarvis,friday}` absent; no live `agents/(jarvis|friday).md`
  refs outside `docs/features/*`. **(stamps)** `Pack: 4-pack`, `Persona: JARVIS`; driver frontmatter
  `name: JARVIS` + orchestrator-desc. **(Gate-1)** passes — `Pack ∈ {1-pack,4-pack}` and invoker is the
  driver. **(Gate-2 — expected NOT clean)** the `<<FILL_ME:` scan reports **exactly the 13 intentional
  template placeholders** (12 in `copilot-instructions.md`: 6 profile fields + 6 required Commands rows; 1 in
  `docs/design.md`) — *any deviation from 13 is the regression to catch* (a stray new sentinel, or an
  accidentally filled/removed template field).

Anders' self-consistency ruling (menu-repo vs install; Bhaskar defect #3 + Pack advisory):
- **Two validity axes.** Install-shape (structure + stamps + **Gate-1**) — the menu repo PASSES and must.
  Consumer-completion (**Gate-2** manual fields) — the menu repo FAILS by design (its unfilled placeholders
  ARE the adopter's prompts). T31 verifies the axis the B-β refactor changed and explicitly excludes Gate-2.
- **Pack stays literal `4-pack`** (Stamped class with hash + Persona; never a sentinel). "Pack unset" ≙
  value ∉ {`1-pack`,`4-pack`}. Residual "raw-copy inherits 4-pack" risk **accepted** — agentify is the sole
  supported installer and enforces no-default at choice time; the failure mode **fails safe** (inherits the
  stricter 4-pack config). A Gate-1 guarantee note documents this.
- **Pack ≡ Persona** = same field class (Stamped literals, agentify-written, preserved on update, never
  Gate-2 sentinels). Only difference: Gate-1 **blocks** on unset Pack (role must be known); Persona is
  **non-blocking** (safe plain-banner fallback).
- **preflight.md** gains: (Gate-2) a prose source-vs-consumer note (documentation, NOT a bypass — every
  checkout runs the same scan); (Gate-1) the precise "Pack unset" set-membership definition + agentify-
  guarantor line.
- **copilot-instructions.md** Pack comment tightened to state Gate-1 blocking semantics; a 3-field-class
  taxonomy sentence added to the profile preamble (**bare word `FILL_ME` only** — this file is Gate-2-scanned).
- **Human-decision items (assumptions made, to validate at commit gate):** (1) ratify the fail-safe raw-copy
  risk — ASSUMED accepted (consistent with R10 / Flag-A=NONE); (2) optional taxonomy sentence — ASSUMED
  included (legibility at point of use).

Reconciliation with T1–T18: T6→T20+T22+T24; T8→T19; T15/16/17→T21/T22; T7 superseded by T27; T18 superseded
by T26; T9→T28; T10–13→T29; retro F4→T30. T1–T4, T14, T5 unchanged (T25 adds Persona beside Pack). New:
T19–T25, T27, T31. Risks R6–R10 (extra load-hop, profile-vs-frontmatter confusion, binder name/desc are
stamps, name↔Persona drift, decoupled skin mismatch — accepted). Deferrals D6–D8 (skinning sub-agents,
persona/pack switching, richer persona contract).
