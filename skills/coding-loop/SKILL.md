---
name: coding-loop
description: The agent loop for all coding work in this repository. Anders designs, JARVIS checks the design and writes the code, Bhaskar and Anders review the PR, Kittu monitors. Do not use Dave.
---

# Coding loop

Use this loop for all coding work in this repository, in all workstreams.

## Roles

| Agent | Model | Reasoning | Job |
|-------|-------|-----------|-----|
| Anders | `gpt-6-astra` | high | Designs new work. Reviews each PR and the overall design. Arbitrates doubts. |
| JARVIS | session model | n/a | Checks the design. Writes the code. Runs basic tests. Raises the PR. |
| Bhaskar | `gpt-6.1-sol` | high | Reviews and validates each PR. |
| Kittu | `gpt-6-luna` | high | Monitors CI, PR reviews and chat messages. Reports action items to JARVIS. |

Do not use Dave in this loop. JARVIS writes all code.

Always set `model` and `reasoning_effort` from the table when you start an agent.

## New work

1. Send the request to Anders. Anders writes the design.
2. JARVIS checks the design against the code and the repository conventions.
3. If JARVIS and Anders agree, continue. If they disagree, stop and ask the human.
4. Product and architecture decisions go to the human (guardrail 6).

## Existing work

1. JARVIS writes the code. Obey the `/yagni` skill. Write the minimum code. Add no speculative code.
2. JARVIS runs the basic checks: build, `go vet`, `gofmt`, and the unit tests of the changed packages.
3. JARVIS commits and raises or updates the PR. Show the PR title and description to the human before
   you post them. Never force push.
   Set auto-complete on the PR. Use the merge strategy that the target branch policy allows and
   that the recent completed PRs use (for example, squash). Delete the source branch on completion.
   Set auto-complete only on a PR that targets trunk. Never set it on a stacked PR.
4. Start these two reviews in parallel:
   - Bhaskar: PR review and validation (build, tests, lint, race tests).
   - Anders: PR review and overall design review.
5. JARVIS fixes the findings. Send each disputed finding to Anders. If JARVIS and Anders still
   disagree, stop and ask the human.
6. Go back to step 2 until both reviews pass.

## Monitoring

Kittu runs in the background while a PR is open. Kittu:

- Monitors CI runs and gate builds.
- Monitors PR review comments and votes.
- Monitors the team chat for messages that need action.
- Reports each action item to JARVIS. Kittu does not edit files, commit or push.

## Disagreements and doubts

Use this rule for a disagreement between team members and for each doubt that JARVIS has.

1. Send the question to Anders (`gpt-6-astra`, high). Give the context, the options, the JARVIS
   choice and its reasons.
2. If JARVIS and Anders agree, continue without the human.
3. If they do not agree, stop that work and wait for the human. Continue only on work that does not
   depend on the answer.
4. In both cases, record the discussion in the session table `arbiter_calls`: topic, positions,
   outcome.
5. Give the human a summary of the discussion points: at the end of the work for agreed items, and
   immediately for items that wait for the human.

## Limits

These rules do not override the guardrails. These items always need the human:

- Product or architecture decisions.
- Text that JARVIS posts in a PR, a work item or a chat.
- Deploys, release pipelines and force pushes.
