---
name: agentify
description: Install this governance framework into a target project (as a 1-pack or 4-pack), or update an existing project's agent-loop artifacts. Interviews the consumer for its profile and gate commands, composes a single self-contained assistant agent file, and stamps the framework version, pack, persona, and model profile.
---

The distribution / versioning mechanism for this framework. Run from a checkout of **agentify** with
the target repo path in hand. Copilot-only scope. Do this on a `vibe/<nnn>-adopt-agentify` branch in the
target — never on its trunk — so the human reviews the diff before merge.

**Design rule — the installed repo has no framework ceremony.** A consumer never loads a role body, a
persona overlay, or a binder. Every agent is exactly one self-contained file in `.github/agents/`.
Composition happens **here, at install time**, from source-only templates that are never copied.

## Ownership model

- **Source-only** (the composition inputs; **never** copied into a consumer): everything under
  `.github/agent-templates/` — `roles/conductor.md`, `roles/solo.md`, `personas/jarvis.md`,
  `personas/friday.md`.
- **Composed** (generated on install, regenerated on update): the assistant
  `.github/agents/<PERSONA>.md` — role body + persona tail + stamped frontmatter, in one file. Never
  hand-edited in the consumer; edit the templates here and re-run.
- **Framework-owned** (refresh on update, **except** their consumer extension regions — see below):
  `AGENTS.md`, the 4-pack sub-agents `.github/agents/anders.md`
  / `dave.md` / `bhaskar.md`, the skills `.github/skills/agentify.md` / `preflight.md` /
  `retrospective.md`, `docs/meta-design.md`, `docs/features/TASK_FILE_TEMPLATE.md`, and the **rules**
  portion of `.github/copilot-instructions.md` (everything *above* the `## Project profile` heading).
- **Stamped** (framework-owned files carrying per-install values that are preserved / re-derived, never
  clobbered on update): the composed assistant's frontmatter `name` (= Persona) and `description`
  (= the pack's role-desc); each installed agent's `model` + `reasoning` (derived from the Model
  profile); and in the Project profile, the *Framework version adopted* hash, **Pack**, **Persona**,
  and **Model profile**.
- **Consumer-owned** (create only if absent, **never** clobber): `docs/design.md`, `docs/backlog.md`,
  `docs/features/<nnn>-*.md`, the **Project profile** block of `.github/copilot-instructions.md`
  (including its **Commands** table), and the two **gate recipes** `.github/skills/build-test.md` +
  `.github/skills/build-test-full.md` — seeded from the framework's recipe and pruned by the install
  interview, then owned by the consumer, because every real stack localizes them.
- **Plumbing** (`.editorconfig`, `.gitignore`, `.gitattributes`, `.vscode/`): create only if absent.

## Consumer extension regions

Framework-owned files are **refreshed** on update, so a consumer that writes its own rule into the
middle of one loses it. Every framework-owned artifact therefore ships with **named, empty extension
regions** at its sanctioned extension points. One mechanism, paired markers, usable anywhere in a file:

    <!-- AGENTIFY:BEGIN rules — consumer-owned; survives `agentify` updates. -->
    <!-- AGENTIFY:END rules -->

Sanctioned anchors:

| File | Region | Holds |
|------|--------|-------|
| `.github/copilot-instructions.md` | `rules` | extra project-wide golden rules (numbered `P1`, `P2`, … so framework numbering stays stable) |
| `.github/agents/*.md` (incl. the composed assistant) | `rules` | extra standing rules for that one agent |
| `.github/agents/*.md` | `learnings` | the `retrospective` skill's *Project learnings* |
| `.github/skills/preflight.md` | `gates` | extra preflight gates beyond Gate 1 / Gate 2 |
| `docs/meta-design.md` | `testing` | this stack's test marking + selection mechanism |
| `AGENTS.md` | `notes` | anything a non-Copilot tool must read |

Rules:

- Content inside a region is **consumer-owned**. Content outside it is framework-owned.
- **Only the anchors in the table above are harvested**, and only in the file that owns them. Never
  discover regions by scanning a file for the marker pattern — several framework files (this one
  included) *document* the markers in prose, and a blind scan would treat those examples as regions.
- Regions are **harvested by name** on update: read the consumer's current file, capture each region's
  body, install the fresh framework file, re-inject each captured body into the matching anchor.
- An anchor present in the consumer but gone from the framework, or vice versa, is **reported to the
  human**, never silently dropped.
- A consumer needing an extension point that doesn't exist raises it upstream; agents do not invent
  new anchors in a consumer.

> **Maintainer note:** when writing *about* these markers outside a real anchor, keep the example
> inside a fenced/indented block and never place a bare `BEGIN`/`END` pair at column 0 in prose.

**Diff-and-confirm (the backstop).** Regions cover the sanctioned cases; they cannot cover a consumer
that edited framework prose directly. So on update, for every framework-owned file, diff the
consumer's current content against the framework baseline **at the recorded *Framework version
adopted* hash**, ignoring region bodies:

    git -C <agentify-dir> show <recorded-hash>:<path>

If they differ, the consumer has hand-edited framework prose. **Stop and show the human that diff**,
ask whether to keep, discard, or migrate it into a region — never overwrite silently. (This is what
makes the recorded hash load-bearing; a consumer without one cannot be updated safely and must be
asked about every framework-owned file it has touched.)

## What it does

1. **Ask which pack and which persona** — pack `1-pack` (solo generalist) or `4-pack` (full team), and
   the assistant's **persona** skin. **No default** for either; the human chooses. The pack selects the
   **role body** (`4-pack ⇒ conductor`, `1-pack ⇒ solo`) and whether the 3 sub-agents ship; the persona
   menu is `.github/agent-templates/personas/` (today `JARVIS` | `FRIDAY`) — **validate the pick ∈ that
   set**. A persona need not match the pack's natural assistant (e.g. a 1-pack skinned `JARVIS`); that's
   allowed — behaviour derives from Pack (role), never the skin. Also **ask which model profile** —
   `mix-1`, `mix-2`, `anthropic`, or `openai` (**default `mix-1`**); see *Model profiles* below.
2. **Interview the Project profile.** Ask for each field rather than leaving the human a wall of
   `FILL_ME`. For every field, "not applicable" is a first-class answer that writes `none`:
   - **Project name**, **Addressing the human** (default `Sir`), **Trunk branch** fallback.
   - **Generated artifacts** — paths/globs no agent may hand-edit (golden rule #5), or `none`.
   - **App run/restart & liveness mechanism** — *first ask whether the project even has one.* Many
     don't (a library, a CLI, an editor extension). If it does, ask **how to (re)start it locally** and
     **what signals it is live**; write both. If it doesn't, write `none` — the assistant then skips
     app-restart duty entirely instead of inventing it.
   - **Language-specific conventions** — the stack rules agents must respect.
   - **CI/CD pipeline** — link/description, or `none`. Agents never deploy either way.
3. **Interview the gate commands** and write the **Commands** table. Required (never `none`): `build`,
   `test:quick`, `test:full`. Then walk the optional gates one at a time — `format:fix`,
   `format:check`, `lint`, `dry-check` (duplication), `mutation-test`, `crap-check` — asking for each:
   **(a) is this relevant to your stack, and (b) if so, what is the exact command?** Leave `none` for
   any the consumer declines. Ask finally whether the stack needs **additional** commands (e.g.
   `restore`, `type-check`, `codegen`) and add rows for them. Encourage filling as many as genuinely
   apply — the more varied the mechanical constraints, the more easily the agents are supervised — but
   record a deliberate `none` (e.g. "the Release build already fails on analyzer diagnostics, so there
   is no separate lint pass") rather than pretending a gate exists.
4. **Interview the testing mechanism** and fill the `testing` region of `docs/meta-design.md`. The
   framework owns the **taxonomy** (unit / integration / acceptance, and their boundaries); the
   consumer owns the **mechanism**. Ask: **(a) how does this stack mark a test's type** (xUnit
   `[Trait("type", …)]`, a Rust `#[cfg(test)]` module vs `tests/`, a pytest marker, a directory
   convention…), and **(b) what filter selects each type** in `test:quick` / `test:full`? Write both
   into the region. Also ask **(c) what mechanical policy checks this stack automates** — linters,
   analyzers, dependency validation — and record them, cross-referencing the Commands rows that run
   them. Golden rule #8 points every agent here, so a wrong answer misleads all of them.
5. **Seed and prune the two gate recipes.** Copy `.github/skills/build-test.md` (fast) and
   `.github/skills/build-test-full.md` (full) from the framework, then **edit each down to
   this stack's reality**: delete every step whose command came back `none`, reorder the survivors into
   the order this stack must run them (e.g. a pre-build `restore` first; a type-aware linter after
   `build`), add rows for any extra commands, and replace the generic "if a gate is `none`…" prompt
   with the consumer's actual reason for each deliberate omission. The recipes name commands from the
   Commands table and never hardcode a shell command. They are consumer-owned from this point — an
   update never overwrites them.
6. **Compose and install the agents** (see *Composition* below), plus the skills, docs, and plumbing,
   per the ownership model.
7. **Stamp the version, pack, persona, and model profile.** Record the current agentify commit hash
   into the target's `.github/copilot-instructions.md` → Project profile → *Framework version adopted*,
   and stamp *Pack*, *Persona*, and *Model profile*:

       git -C <agentify-dir> rev-parse HEAD

8. **Report remaining placeholders.** Run the target's `.github/skills/preflight.md` placeholder scan
   and list every unfilled `<<FILL_ME:` placeholder the human must still complete — after a full
   interview this should be only `docs/design.md`.

## Composition — building `.github/agents/<PERSONA>.md`

One file, no indirection. Build it in this order:

1. **Pick the sources.** `role = 4-pack ? roles/conductor.md : roles/solo.md`;
   `tail = personas/<persona lower-cased>.md`. Both live under `.github/agent-templates/`.
2. **Strip provenance.** Remove the leading `<!-- … -->` comment block from *each* source, **together
   with the blank line immediately following it** — it documents the template and must not appear in
   the consumer's file.
3. **Substitute.** In the role body, replace every `{{PERSONA}}` with the persona name **upper-cased**
   (e.g. `JARVIS`). That is the only token substituted.
4. **Emit frontmatter**, then the substituted role body, then the persona tail verbatim, then the
   preserved-region block (step 5). Separate each of the four parts by **exactly one** blank line, and
   trim trailing whitespace from each part first:

       ---
       name: <PERSONA>
       description: <the pack's role-desc, below>
       model: <per Model profiles, below>
       reasoning: max
       ---

5. **Append the `learnings` region** at the very end of the file. (The `rules` region already arrives
   with the role body, at the end of its *Boundaries* section — see *Consumer extension regions*.)

       <!-- AGENTIFY:BEGIN learnings — consumer-owned; survives `agentify` updates. -->

       ## Project learnings

       _Durable, project-specific notes for this agent, appended by the `retrospective` skill. Keep them
       short and high-signal; the governance above always wins._

       <!-- AGENTIFY:END learnings -->

6. **Write** to `<target>/.github/agents/<PERSONA>.md`. Both the frontmatter `name` and the filename
   stem are the persona **upper-cased** (`JARVIS.md`, `FRIDAY.md`); prose elsewhere writes
   `<Persona>` for the same thing.
7. **Verify** the result:
   - contains no `{{PERSONA}}` and no `SOURCE-ONLY template` string;
   - its only HTML comments are the four region markers (`BEGIN`/`END` × `rules`, `learnings`), each
     `BEGIN` matched by an `END` of the same name;
   - contains **exactly one** `# <PERSONA> etiquette` heading;
   - frontmatter `name` equals the filename stem, and `description` equals the pack's role-desc
     verbatim.

> **Link invariant for role templates.** A role body is authored for its **composed** location,
> `.github/agents/<PERSONA>.md` — not for its source path. Any file-relative link inside
> `agent-templates/roles/*.md` (e.g. `../../docs/meta-design.md`) must resolve from `.github/agents/`.
> Prefer repo-root-relative paths in prose; use a file-relative form only for a clickable Markdown
> link, and check it against the composed depth.

Role-descs (the frontmatter `description`):

- **4-pack / conductor** — *Runs the agentic loop (hub-and-spoke). Coordinates Dave, Bhaskar, and
  Anders. Read-only inspection + git/task-file management only; never designs, codes, or verifies.*
- **1-pack / solo** — *Solo generalist for the 1-pack: designs, implements, verifies, and reviews in
  one context; owns git + the task file. Never deploys.*

Reject a persona whose lower-cased name collides with a sub-agent filename (`anders`, `dave`,
`bhaskar`).

## Packs

Every install ships the same core — `copilot-instructions.md`, the 3 framework skills, the 2 generated
gate recipes, docs, plumbing, `AGENTS.md`, and **one composed assistant**. The packs differ only in
which role body composes in and whether the sub-agents ship.

### 4-pack
Composes `roles/conductor.md`; also copies the **3 sub-agents** `anders.md` / `dave.md` /
`bhaskar.md`. Full hub-and-spoke team with strict separation of duties.

### 1-pack
Composes `roles/solo.md`. **No** sub-agents. Lighter on tokens; separation of duties is waived (the
solo assistant wears every hat).

## Model profiles

Every agent runs at **maximum reasoning** (`reasoning: max`); the profile only picks the **vendor** of
each role's MAX model. Per-vendor MAX SKUs: Anthropic `Claude Opus 5 (copilot)`, OpenAI
`GPT-5.6 Sol (copilot)`.

| Role — file | `mix-1` (default) | `mix-2` | `anthropic` | `openai` |
|-------------|-------------------|---------|-------------|----------|
| Architect — `agents/anders.md`      | Opus 5      | GPT-5.6 Sol | Opus 5 | GPT-5.6 Sol |
| Coder — `agents/dave.md`            | Opus 5      | GPT-5.6 Sol | Opus 5 | GPT-5.6 Sol |
| Verifier — `agents/bhaskar.md`      | GPT-5.6 Sol | Opus 5      | Opus 5 | GPT-5.6 Sol |
| Assistant — `agents/<PERSONA>.md`   | GPT-5.6 Sol | Opus 5      | Opus 5 | GPT-5.6 Sol |

- **`mix-1`** (recommended) — Opus 5 designs & codes, GPT-5.6 Sol verifies & drives.
- **`mix-2`** — `mix-1` flipped.
- **`anthropic`** / **`openai`** — every agent on one vendor.

`mix-1` / `mix-2` keep **coder ≠ verifier vendor** so the independent check doesn't inherit the coder's
blind spots. In a **1-pack** only the assistant ships, so a mix collapses to that one agent's vendor —
the cross-vendor benefit is a 4-pack property.

## Install (new target) · PowerShell sketch

> **New target only.** For a repo that already adopted the framework, use **Update** below — never
> re-run Install.

    $src = "<agentify-dir>"; $dst = "<target-repo>"
    $pack = "<1-pack | 4-pack>"; $persona = "<JARVIS | FRIDAY>"; $modelProfile = "mix-1"
    if ($pack -notin '1-pack','4-pack') { throw "Pick a pack explicitly: '1-pack' or '4-pack' (no default)." }
    $menu = (Get-ChildItem "$src/.github/agent-templates/personas/*.md" | % BaseName)   # jarvis, friday
    if ($persona.ToLower() -notin $menu) { throw "Pick a persona from agent-templates/personas (no default): $($menu -join ', ')." }
    if ($persona.ToLower() -in 'anders','dave','bhaskar') { throw "Persona '$persona' collides with a sub-agent filename; choose another." }
    if ($modelProfile -notin 'mix-1','mix-2','anthropic','openai') { throw "Pick a model profile: mix-1 | mix-2 | anthropic | openai (default mix-1)." }
    $maxA = 'Claude Opus 5 (copilot)'; $maxO = 'GPT-5.6 Sol (copilot)'   # reasoning is 'max' for every role
    $models = switch ($modelProfile) {                                    # authoritative stamp map
        'mix-1'     { @{ anders=$maxA; dave=$maxA; bhaskar=$maxO; assistant=$maxO } }
        'mix-2'     { @{ anders=$maxO; dave=$maxO; bhaskar=$maxA; assistant=$maxA } }
        'anthropic' { @{ anders=$maxA; dave=$maxA; bhaskar=$maxA; assistant=$maxA } }
        'openai'    { @{ anders=$maxO; dave=$maxO; bhaskar=$maxO; assistant=$maxO } }
    }
    Copy-Item "$src/AGENTS.md" $dst                                   # framework-owned: always
    foreach ($p in '.editorconfig','.gitignore','.gitattributes') {   # plumbing: only if absent
        if (-not (Test-Path "$dst/$p")) { Copy-Item "$src/$p" $dst }
    }
    if (-not (Test-Path "$dst/.vscode")) { Copy-Item "$src/.vscode" $dst -Recurse }
    New-Item "$dst/.github/agents","$dst/.github/skills" -ItemType Directory -Force
    Copy-Item "$src/.github/copilot-instructions.md" "$dst/.github"
    Copy-Item "$src/.github/skills/agentify.md","$src/.github/skills/preflight.md", `
              "$src/.github/skills/retrospective.md" "$dst/.github/skills" -Force
    if (-not (Test-Path "$dst/.github/skills/build-test.md")) {      # seeds only; pruned by the
        Copy-Item "$src/.github/skills/build-test.md",              # interview, consumer-owned after
                  "$src/.github/skills/build-test-full.md" "$dst/.github/skills"
    }
    if ($pack -eq '4-pack') {
        Copy-Item "$src/.github/agents/anders.md","$src/.github/agents/dave.md", `
                  "$src/.github/agents/bhaskar.md" "$dst/.github/agents"
    }
    # Compose the assistant (see *Composition*): role body + persona tail -> agents/<PERSONA>.md.
    # agent-templates/ itself is NEVER copied into $dst.
    New-Item "$dst/docs/features" -ItemType Directory -Force
    Copy-Item "$src/docs/meta-design.md" "$dst/docs"
    Copy-Item "$src/docs/features/TASK_FILE_TEMPLATE.md" "$dst/docs/features"
    if (-not (Test-Path "$dst/docs/design.md"))  { Copy-Item "$src/docs/design.md"  "$dst/docs" }
    if (-not (Test-Path "$dst/docs/backlog.md")) { Copy-Item "$src/docs/backlog.md" "$dst/docs" }
    # then: interview the profile + commands (steps 2-3); write both gate recipes (step 4);
    #       stamp Pack/Persona/Model profile + the agentify hash (step 6); run preflight (step 7)

## Update (existing target)

Refresh framework-owned files only, and **recompose** the assistant from the current templates. In
this order:

1. **Resolve the stamps.** Read *Pack*, *Persona*, and *Model profile* from the Project profile.
   A missing or unrecognized **Pack** or **Persona** is **asked**, never defaulted — they are no-default
   by design. A missing **Model profile** falls back to `mix-1` (the only one of the three with a
   declared default). An absent *Framework version adopted* means the adopter predates stamping: treat
   every framework field as new for the Splice step and re-stamp the hash.
2. **Harvest the extension regions.** Before touching anything, read every framework-owned file in the
   target and capture each `<!-- AGENTIFY:BEGIN <name> … -->` … `<!-- AGENTIFY:END <name> -->` body.
   Those are the consumer's, and get re-injected after refreshing.
3. **Diff-and-confirm outside the regions.** For each framework-owned file, compare the consumer's
   current content (region bodies blanked) against `git show <recorded-hash>:<path>` from this
   checkout. Any difference means the consumer hand-edited framework prose: **stop, show the diff, and
   ask** whether to keep it, discard it, or migrate it into an extension region. Never overwrite
   silently. A target with **no** recorded hash gets asked about every framework-owned file it has
   modified.
4. **Recompose** `.github/agents/<PERSONA>.md` from `.github/agent-templates/` using the resolved Pack +
   Persona, re-stamping frontmatter `name` / `description` / `model` / `reasoning`, and re-injecting
   that file's harvested `rules` and `learnings` regions.
5. **Remove any orphaned assistant** — unconditionally, not only during legacy cleanup. After
   recomposing, `git rm` every `.github/agents/*.md` whose stem is not `<PERSONA>` / `anders` / `dave` /
   `bhaskar`. This is what makes a **Persona change** (e.g. JARVIS → FRIDAY) safe; leaving the old file
   behind would break preflight Gate 1's single-assistant premise. Carry its harvested regions into the
   recomposed assistant first.
6. **Refresh** the 3 framework skills, `docs/meta-design.md`, `docs/features/TASK_FILE_TEMPLATE.md`,
   `AGENTS.md`, and the **rules** portion of `copilot-instructions.md` — then re-inject the harvested
   regions. A file that gained a new anchor since the recorded hash gets it empty; an anchor the
   consumer has but the framework no longer ships is **reported**, never dropped.
7. **Reconcile the sub-agents against Pack** — never unconditionally. `4-pack`: refresh
   `anders.md` / `dave.md` / `bhaskar.md` (adding them if a `1-pack → 4-pack` switch). `1-pack`: they
   must **not** exist — `git rm` them on a `4-pack → 1-pack` switch, because the solo assistant's
   golden-rule-#2 waiver is predicated on their absence.
8. **Preserve** every consumer-owned file and value: the whole Project profile including the Commands
   table, `docs/design.md`, `docs/backlog.md`, the feature docs, and **both gate recipes**.
9. **Splice** into the consumer's existing `## Project profile` only those framework fields/table-rows
   introduced **since the recorded *Framework version adopted* hash** — never "every row the adopter
   lacks". A consumer may **delete** an optional Commands row outright rather than carry it as `none`;
   re-adding it on every update would be an endless fight. (With no recorded hash, ask before adding
   anything.)
10. **Re-stamp** the hash (and Pack / Persona / Model profile if they changed), then re-run preflight.

> **Legacy layout cleanup (required on update).** Earlier versions of this framework installed a
> *binder* plus separate role and persona files. If the target has any of `.github/agent-roles/`,
> `.github/personas/`, `.github/agent-templates/`, or a binder at `agents/driver.md` /
> `agents/assistant.md`, then: compose `agents/<PERSONA>.md` as above and `git rm -r` those
> directories. Carry any consumer-authored content from the old role body or overlay into the
> templates here (not into the consumer) before deleting. (The orphaned-assistant sweep is an
> unconditional Update step above, so it covers this case too.) Idempotent: a no-op once only the
> composed assistant and the sub-agents remain.

> **Legacy marker cleanup (required on update).** An earlier version used a one-sided
> `<!-- AGENTIFY:PRESERVE … -->` tail marker in agent files. Convert it: everything below it becomes
> the body of a `learnings` region, wrapped in the paired `BEGIN learnings` / `END learnings` markers.
> Add an empty `rules` region at the same time.

> **Legacy Commands cleanup (required on update).** `lint`, `format:fix`, and `format:check` were once
> **required** rows. They are now optional and may be `none` **or deleted**. Leave a consumer's
> existing values alone and never re-add a row it removed; only re-label the `Required` column on rows
> that are still present. A consumer still carrying inline command values inside `build-test.md` /
> `build-test-full.md` migrates them into the Commands table first, then keeps its recipes (they are
> consumer-owned — do not overwrite them).
