# Meta design

How to design features in any repository that adopts this framework.

## Work

The human provides the requirements. The installed workflow sets the branch, working root, and
work-record format described below.

## Delivering work

Deliver work in **slices**. Each slice is a complete change that you can deploy on its own and
verify end to end. In rare cases, you can split a slice (for example, into frontend and backend).
Ask the human first.

## Writing tests

Classify tests by boundary, not by how long they take.

| Type | Boundary and purpose | Runs in | Example xUnit marking/filter |
|------|----------------------|---------|------------------------------|
| Preflight | Linters, analyzers, dependency validation, and similar policy checks. | Hundreds per minute | Stack-specific |
| Unit | Fine-grained and fast. Does not cross a process boundary. | Thousands per minute | `[Trait("type", "UnitTests")]`; `type=UnitTests` |
| Integration | Checks critical integration between cohesive components. May cross process or network boundaries. | Tens per minute | `[Trait("type", "IntegrationTests")]`; `type=IntegrationTests` |
| Acceptance | Runs critical end-to-end customer scenarios the way a customer would. | One or two minutes each | `[Trait("type", "AcceptanceTests")]`; `type=AcceptanceTests` |

The run rates show direction only. They are not criteria, quotas, or limits. The xUnit values are
examples; use the stack's native mechanism. Specialized suites add to these categories. Automated
tests do not replace exploratory testing.

Here, "preflight" means mechanical policy checks, not the loop-start gates in
`.github/skills/preflight.md`. Record their commands in the Commands table.

Do not add tests that scan source files. Use runtime metadata or reflection instead. If neither is
available, leave the policy unenforced.

### This stack's testing mechanism

_Record how tests declare each type, how gates select them, and which mechanical checks run._

## Designing work

A design has these parts (x is a number):

- **Design options (Ox):** each with pros and cons, plus which one we recommend and why.
- **Slices (Sx):** as described above.
- **Tasks (Tx):** one or more for each slice.
- **Risks (Rx):** for the whole design.
- **Assumptions (Ax):** for the whole design.
- **Deferrals (Dx):** for the whole design.

The options analysis during planning can include more detail (summary, affected layers, risk, and
effort). Only the pros, cons, and recommendation are saved. Use the sections of the selected work
record. Do not create a second record in the other workflow's format.

## Capturing user-tagged work

Follow "User task markers" in `.github/copilot-instructions.md`.
`LIM:` items go in `docs/backlog.md` for future prioritization, not in the current session's queue.
Track `TODO:` items in the session task list and in the active record, using its task format below.
Keep the tag and status. Report unresolved session work at handoff.

## Starting work

<!-- WORKFLOW:FEATURE:BEGIN -->
### Feature workflow

Each feature is saved as `docs/features/<nnn>-<feature_name>.md`. `<nnn>` is a three-digit sequence
number with leading zeros, assigned in creation order (next = highest existing + 1), so feature docs
sort by date. The numbers are a stable index. Never renumber existing docs. The working branch has
the same name: `vibe/<nnn>-<feature_name>`. `TASK_FILE_TEMPLATE.md` is exempt.

After the design is approved, create that branch from the latest trunk in the chosen checkout. Never
discard uncommitted changes to switch branches. Create the feature record from
`docs/features/TASK_FILE_TEMPLATE.md`. To resume, use the existing branch and its record.
Keep task status and decisions current. Mark the record Complete when its tasks are done.
Include human-tagged `TODO:` items in Tasks, with their status.
<!-- WORKFLOW:FEATURE:END -->

<!-- WORKFLOW:WORKTREE:BEGIN -->
### Worktree workflow

A named work item uses a kebab-case `<id>`, a linked worktree on `wi/<id>`, and `work/<id>.md`.
There is no number sequence and no parallel feature file. Read-only questions do not create
worktrees or records.

After the design is approved:

1. Use `git worktree list --porcelain` to find the main checkout and existing worktrees.
2. Confirm the item ID with the human. If its branch, worktree, or record already exists, ask to
   resume it. Never overwrite it or silently create a second copy.
3. Create `wi/<id>` from the latest trunk in a separate linked worktree. Use the project's approved
   helper, or run native `git worktree add -b` from the main checkout. The default location is the
   sibling `<main-checkout-name>-wt/<id>` directory, unless the project sets another root.
4. Create `work/<id>.md` from `work/WORK_ITEM_TEMPLATE.md` inside that worktree.

When you resume, confirm that the branch and log belong to the selected linked worktree. A `wi/<id>`
branch in the main checkout does not satisfy this workflow. Resolve the working root before you edit.
Use absolute paths and `git -C` where tool sessions do not keep directory changes.

Keep the question, deliverable, and agreed design in Definition. Keep dated progress and task status
in Progress. Keep corrections and dead ends in Learnings. Keep produced paths in Artifacts. Keep
unresolved issues in Open. Update the log as the work moves forward, and carry forward relevant
earlier findings. Mark its status Complete when its tasks are done. Edit only this item's log.
Include human-tagged `TODO:` items in Progress, with their status.

Never switch the main checkout to an item branch, move another item's changes, merge to trunk, or
remove a worktree without the human's approval.
<!-- WORKFLOW:WORKTREE:END -->
