# Meta design

How feature design is done in any repo that adopts this framework.

## Work

The human provides the requirements. The installed workflow determines the branch, working root
and work-record format below.

## Delivering work

Deliver work in **Slices**. Each slice is a full, independently deployable and
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

## Designing work

Design has the following concepts (x is a number):

- **Design options (Ox)** — each with pros/cons, which one we recommend & why.
- **Slices (Sx)** — as described above.
- **Tasks (Tx)** — one or more per slice.
- **Risks (Rx)** — overall.
- **Assumptions (Ax)** — overall.
- **Deferrals (Dx)** — overall.

The planning-time options analysis may be richer (summary, affected layers, risk, effort); only
pros/cons + recommendation are persisted. Use the selected work record's sections; do not create
a second record with the other workflow's format.

## Starting work

<!-- WORKFLOW:FEATURE:BEGIN -->
### Feature workflow

Each feature is persisted as `docs/features/<nnn>-<feature_name>.md`. `<nnn>` is a 3-digit
zero-padded sequence number assigned in creation order (next = highest existing + 1), so feature docs
sort chronologically. Numbers are a stable index — never renumber existing docs. The working branch
matches: `vibe/<nnn>-<feature_name>`. `TASK_FILE_TEMPLATE.md` is exempt.

After design approval, create that branch from the latest trunk in the chosen checkout. Never
discard uncommitted changes to switch branches. Create the feature record from
`docs/features/TASK_FILE_TEMPLATE.md`. To resume, use the existing branch and its record.
Keep task status and decisions current; mark the record Complete when its tasks are done.
<!-- WORKFLOW:FEATURE:END -->

<!-- WORKFLOW:WORKTREE:BEGIN -->
### Worktree workflow

A named work item uses a kebab-case `<id>`, a linked worktree on `wi/<id>`, and `work/<id>.md`.
There is no numeric sequence or parallel feature file. Read-only questions do not create worktrees
or records.

After design approval:

1. Use `git worktree list --porcelain` to identify the main checkout and existing worktrees.
2. Confirm the item ID with the human. If its branch, worktree or record already exists, ask to resume
   it; never overwrite it or silently create a second copy.
3. Create `wi/<id>` from the latest trunk in a separate linked worktree. Use the project's approved
   helper, or native `git worktree add -b` from the main checkout. Default location is the sibling
   `<main-checkout-name>-wt/<id>` directory unless the project specifies another root.
4. Create `work/<id>.md` from `work/WORK_ITEM_TEMPLATE.md` inside that worktree.

When resuming, confirm that the branch and log belong to the selected linked worktree. A `wi/<id>`
branch in the main checkout does not satisfy this workflow. Resolve the working root before edits;
use absolute paths and `git -C` where tool sessions do not preserve directory changes.

Keep the question, deliverable and agreed design in Definition; dated progress and task status in
Progress; corrections and dead ends in Learnings; produced paths in Artifacts; unresolved issues in
Open. Update the log as work proceeds and carry forward relevant earlier findings. Mark its status
Complete when its tasks are done. Edit only this item's log.

Never switch the main checkout to an item branch, move another item's changes, merge to trunk or
remove a worktree without the human's approval.
<!-- WORKFLOW:WORKTREE:END -->
