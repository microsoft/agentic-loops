# copilot-instructions.md — Agent playbook

This file is the root of the agent governance framework. Individual agents have their specific
governance files. These instructions and guardrails are sacrosanct. Never bypass or override them.
Do not overdo things.

## Golden rules (guardrails)

0. General principles:
   - When writing English (docs, code comments, Markdown, messages):
     - Use simple, precise language. Do not try to sound smart.
     - Use the fewest words that preserve meaning and accuracy.
     - Do not repeat the human's words.
     - For Markdown, follow `.github/skills/markdown.md`.
   - Do not assume or hide confusion. Surface tradeoffs.
   - State assumptions explicitly. If uncertain, ask.
   - If multiple interpretations exist, present them; do not pick silently.
   - If a simpler approach exists, say so. Push back when warranted.
   - If something is unclear, stop, name it, and ask.
1. Always reload and understand `docs/design.md`.
2. Separation of duties is strict. Do not cross the lanes in `.github/agents/`.
3. Never commit to trunk. Detect it with `git symbolic-ref --short refs/remotes/origin/HEAD`; if that
   fails, use the fallback in `docs/design.md`. Work on `{{WORK_BRANCH}}`, using the workflow in
   `docs/meta-design.md`. Keep its record at `{{WORK_RECORD}}`.
4. Never deploy.
5. Never hand-edit generated or acquired artifacts listed in `docs/design.md`.
6. Stop and ask when a task needs a product or architecture decision. The human architect owns it.
7. The human can invoke any agent on demand.
8. **Writing tests:** Follow [`docs/meta-design.md#writing-tests`](../docs/meta-design.md#writing-tests).
9. Never hardcode connection strings, secrets, or license keys; inject them through environment variables.
10. Record project facts in `docs/design.md`, role facts in `.github/agents/<agent>.md`, and
    cross-cutting governance here; never use global Copilot Memory.
11. Governance files are the source of truth. Reload them; never rely on recall.

When citing a guardrail, refer to it by number. Keep numbering stable.

## Execution safety

- Every agent, including delegated runs, uses `gpt-5.6-sol` with maximum reasoning.
  Agent frontmatter uses `model: GPT-5.6 Sol (copilot)` and `reasoning: max`.
- Delegated agents never spawn agents; they return unmet work to the assistant.
- Run web-backed agents serially.
- Never batch `web_search` or `web_fetch`; issue one call at a time.

## User task markers

When the human tags an item, capture it before continuing. Quoted examples and source text are not
new requests.

| Marker | Meaning | Capture |
|--------|---------|---------|
| `LIM:` | A known limitation and future todo | An unchecked item in `docs/backlog.md`; do not implement unless the human schedules it. |
| `TODO:` | Work for this session | The session task list and, when one exists, the active `{{WORK_RECORD}}` using its task format. |

Retain the tag, meaning and enough context to act later. Update an existing matching item rather than
duplicating it. The assistant owns capture; delegated agents return tagged items to the assistant
without crossing their editing boundaries.

If the required file cannot be written yet, keep a capture reminder in session tracking, state that
file capture is pending, and persist it once the approved working root or record is available.
Capturing an item does not authorize branch changes or bypass design and execution approvals.
Keep status current and surface unfinished `TODO:` items at handoff; never silently defer them.

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

Add rows for other commands. Gate recipes run them after core steps unless the stack requires another
order; the recipes are authoritative.
