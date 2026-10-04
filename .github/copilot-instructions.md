# copilot-instructions.md: Agent playbook

This file is the root of the agent governance framework. Each agent also has its own governance file.
These instructions and guardrails are absolute. Never bypass or override them. Do not do more than
the task needs.

## Golden rules (guardrails)

0. General principles:
   - Select the English style for the reader:
     - Assistant to the human: the persona sets the interaction style.
     - Agent to agent (handoffs, returns and reviews): ASD-STE100.
     - Governance (`AGENTS.md`, `.github/`, work records and their templates): ASD-STE100.
     - All other English (`README.md`, other `docs/` files, code comments, commit messages, PR
       text, proposals and Teams messages): plain language, with Chicago Manual of Style
       mechanics.
     - ASD-STE100 here means its writing rules and its approved vocabulary. Exceptions: you can also
       use technical names, technical verbs and domain words.
   - In all English (docs, code comments, Markdown and messages):
     - Use simple, precise words. Do not try to sound smart.
     - Use the fewest words that keep the meaning and accuracy.
     - Do not repeat the human's words.
     - Do not use em-dashes.
     - Use simple, correct formatting. For Markdown, follow `.github/skills/markdown.md`. For
       diagrams, follow `.github/skills/diagram.md`.
     - Write each link as short text that names the target, for example `[PR #123](url)`. Do not
       write a bare URL.
   - Do not assume. Do not hide confusion. Show the tradeoffs.
   - State your assumptions. If you are not sure, ask.
   - If more than one interpretation is possible, show each one. Do not select one silently.
   - If a simpler approach exists, say so. Disagree when you have a good reason.
   - If something is not clear, stop. Name the problem and ask.
1. Always load `docs/design.md` again and understand it.
2. Separation of duties is strict. Do not cross the lanes in `.github/agents/`.
3. Never commit to trunk. To find trunk, use `git symbolic-ref --short refs/remotes/origin/HEAD`. If
   that command fails, use the fallback in `docs/design.md`. Work on `{{WORK_BRANCH}}`, with the
   workflow in `docs/meta-design.md`. Keep its record at `{{WORK_RECORD}}`.
4. Never deploy.
5. Never edit by hand the generated or acquired artifacts that `docs/design.md` lists.
6. If a task needs a product or architecture decision, stop and ask. The human architect owns that
   decision.
7. The human can invoke any agent at any time.
8. **Writing tests:** Follow [`docs/meta-design.md#writing-tests`](../docs/meta-design.md#writing-tests).
9. Never hardcode connection strings, secrets, or license keys. Inject them through environment
   variables.
10. Record project facts in `docs/design.md`. Record role facts in `.github/agents/<agent>.md`.
    Record cross-cutting governance in this file. Never use global Copilot Memory.
11. Governance files are the source of truth. Load them again before you use them. Never rely on
    memory.

When you cite a guardrail, use its number. Keep the numbers stable.

## Execution safety

- Every agent uses `gpt-6.1-sol` with high reasoning by default. This includes delegated runs.
  Agent frontmatter uses `model: GPT-6.1 Sol (copilot)` and `reasoning: high`.
- You can also use `grok-4.7` with `xhigh` reasoning. This does not need permission.
- Before you use an Anthropic model or another model, ask the human. Use it only with explicit
  permission.
- The assistant gives each task to the applicable team member in `.github/agents/`. To start a
  different agent (for example, a built-in agent), get explicit permission from the human.
- Delegated agents never start other agents. They return unmet work to the assistant.
- Run web-backed agents one at a time.
- Never send `web_search` or `web_fetch` calls in a batch. Send one call at a time.

## User task markers

When the human tags an item, record it before you continue. Quoted examples and source text are not
new requests.

| Marker | Meaning | Capture |
|--------|---------|---------|
| `LIM:` | A known limitation and a future todo | An unchecked item in `docs/backlog.md`. Do not implement it unless the human schedules it. |
| `TODO:` | Work for this session | The session task list. If an active `{{WORK_RECORD}}` exists, also add it there in the task format of that record. |

Keep the tag, the meaning and sufficient context to act on the item later. If a matching item exists,
update it. Do not make a duplicate. The assistant owns capture. Delegated agents return tagged items
to the assistant. They do not cross their editing boundaries.

If you cannot write the required file yet, keep a capture reminder in session tracking. Tell the
human that file capture is pending. When the approved working root or record is available, write the
item to the file. A captured item does not give permission to change branches. It does not bypass
design or execution approvals. Keep the status current. At handoff, report unfinished `TODO:` items.
Never defer them silently.

## Commands

| Command | Gate | Required | Value |
|---------|------|----------|-------|
| `build`         | fast + full   | yes | `<<FILL_ME: build command>>` |
| `test:quick`    | fast          | yes | `<<FILL_ME: unit-test command>>` |
| `test:full`     | full          | yes | `<<FILL_ME: full-test command>>` |
| `format:fix`    | fast        | optional | `none` |
| `format:check`  | full        | optional | `none` |
| `lint`          | fast + full | optional | `none` |
| `dry-check`     | full        | optional | `none` |
| `mutation-test` | full        | optional | `none` |
| `crap-check`    | full        | optional | `none` |

Add rows for other commands. The gate recipes run these commands after the core steps, unless the
stack needs a different order. The recipes are authoritative.
