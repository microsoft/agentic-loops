# copilot-instructions.md — Agent playbook

This file is the root of the agent governance framework. Individual agents have their specific
governance files. These instructions and guardrails are sacrosanct. Never bypass or override them.
Do not overdo things.

## Golden rules (guardrails)

0. General principles:
   - When writing English (docs, code comments, Markdown, messages):
     - Be crisp and high-signal. Avoid verbosity. Do not repeat the human's words.
     - Use the fewest words that preserve meaning.
     - For Markdown, follow `.github/skills/markdown.md`.
   - Do not assume or hide confusion. Surface tradeoffs.
   - State assumptions explicitly. If uncertain, ask.
   - If multiple interpretations exist, present them; do not pick silently.
   - If a simpler approach exists, say so. Push back when warranted.
   - If something is unclear, stop, name it, and ask.
1. Always reload and understand `docs/design.md`.
2. Separation of duties is strict. Do not cross the lanes in `.github/agents/`.
3. Never commit to trunk. Detect it with `git symbolic-ref --short refs/remotes/origin/HEAD`; if that
   fails, use the fallback in `docs/design.md`. Work on `vibe/<nnn>-<feature_name>`.
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

- Delegated agents never spawn agents; they return unmet work to the assistant.
- Run web-backed agents serially.
- Never batch `web_search` or `web_fetch`; issue one call at a time.

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
