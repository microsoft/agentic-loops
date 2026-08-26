# Meta design

How feature design is done in any repo that adopts this framework.

## Feature

The human provides the requirements that constitute a feature.

## Delivering a feature

Delivering a feature is done in **Slices**. Each slice is a full, independently deployable and
end-to-end verifiable change. Rarely, a slice is split (e.g. frontend/backend) — consult the human first.

## Writing tests

Classify tests by boundary, not duration. The **taxonomy** below is framework-owned and universal; the
**mechanism** — how this stack marks a test's type and how each gate selects it — is stack-specific and
lives in the `testing` region at the end of this section.

| Type | Boundary and purpose | Directional scale |
|------|----------------------|-------------------|
| Unit | Fine-grained, fast, and does not cross a process boundary. | Thousands |
| Integration | Validates critical integration between cohesive components; may cross process or network boundaries. | Hundreds |
| Acceptance | Exercises critical end-to-end customer scenarios by performing actions and verifying outcomes as a customer would. | Tens |

The scales are directional guidelines, not classification criteria or hard quotas. Specialized suites
supplement these categories, and automated tests do not replace team exploratory testing. Do not
specify numeric time limits.

**Mechanical policy checks** — linters, analyzers, dependency validation, formatting — automate policy
so the correct path is the easiest path. They are a *separate concern* from the loop gates enforced by
`.github/skills/preflight.md`; both are recorded below and wired through **Project profile → Commands**.

<!-- AGENTIFY:BEGIN testing — consumer-owned; survives `agentify` updates. -->

### This stack's testing mechanism

_Filled by the `agentify` install interview. Record: (a) how a test declares its type, (b) the filter
each gate uses to select that type, and (c) which mechanical policy checks this stack automates and
which Commands row runs each._

<!-- AGENTIFY:END testing -->

## Designing a feature

Feature design has the following meta-structure (x is a number):

- **Design options (Ox)** — each with pros/cons, which one we recommend & why.
- **Slices (Sx)** — as described above.
- **Tasks (Tx)** — one or more per slice.
- **Risks (Rx)** — overall.
- **Assumptions (Ax)** — overall.
- **Deferrals (Dx)** — overall.

The planning-time options analysis may be richer (summary, affected layers, risk, effort); only
pros/cons + recommendation are persisted. The persisted feature file also carries Requirements
(input) and Notes.

### Naming & numbering

Each feature is persisted as `docs/features/<nnn>-<feature_name>.md`. `<nnn>` is a 3-digit
zero-padded sequence number assigned in creation order (next = highest existing + 1), so feature docs
sort chronologically. Numbers are a stable index — never renumber existing docs. The working branch
matches: `vibe/<nnn>-<feature_name>`. `TASK_FILE_TEMPLATE.md` is exempt.
