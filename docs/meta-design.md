# Meta design

How feature design is done in any repo that adopts this framework.

## Feature

The human provides the requirements that constitute a feature.

## Delivering a feature

Delivering a feature is done in **Slices**. Each slice is a full, independently deployable and
end-to-end verifiable change. Rarely, a slice is split (e.g. frontend/backend) — consult the human first.

## Writing tests

Classify tests by boundary, not duration.

| Type | Boundary and purpose | Runs in | Example xUnit marking/filter |
|------|----------------------|---------|------------------------------|
| Preflight | Linters, analyzers, dependency validation, and similar policy checks. | Hundreds per minute | Stack-specific |
| Unit | Fine-grained, fast, and does not cross a process boundary. | Thousands per minute | `[Trait("type", "UnitTests")]`; `type=UnitTests` |
| Integration | Validates critical integration between cohesive components; may cross process or network boundaries. | Tens per minute | `[Trait("type", "IntegrationTests")]`; `type=IntegrationTests` |
| Acceptance | Exercises critical end-to-end customer scenarios as a customer would. | One or two minutes each | `[Trait("type", "AcceptanceTests")]`; `type=AcceptanceTests` |

Run rates are directional, not criteria, quotas, or limits. xUnit values are examples; use the
stack's native mechanism. Specialized suites supplement these categories, and automated tests do not
replace exploratory testing.

Here, preflight means mechanical policy checks, not `.github/skills/preflight.md` loop-start gates.
Record their commands in the Commands table.

Do not add tests that scan source files. Prefer runtime metadata or reflection; if unavailable, leave
the policy unenforced.

### This stack's testing mechanism

_Record how tests declare each type, how gates select them, and which mechanical checks run._

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
