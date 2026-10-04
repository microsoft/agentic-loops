---
name: yagni
description: Keep design and code simple, and communicate clearly in documents, comments, messages and diagrams.
---

# /yagni

Obey these rules for the remainder of this session.

## Design

- Do not overdesign.
- Review at the codebase and product level for global consistency, integrity and optimization.
- Review against the repository conventions. Where they apply, use Clean Architecture, YAGNI, DRY, SOLID and dependency-flow rules.
- Follow the established patterns and conventions. If a design is more elegant, more DRY/SOLID, has better performance or is more secure, and the change is justified, suggest it. The human makes the final decision on each design change.
- If an item is really a product decision, flag it and give it back to the human.

Also see the [agentic-loops meta-design](https://github.com/microsoft/agentic-loops/blob/master/docs/meta-design.md).

## Code

- Write the minimum code that solves the problem. Write nothing speculative.
- Add no features that the request does not include. Add no abstractions for code that has only one use.
- Add no flexibility or configurability that the human did not request. Add no error handling for scenarios that cannot occur.
- If you write 200 lines and 50 lines are sufficient, rewrite it.
- Ask: "Would a senior engineer say this is overcomplicated?" If yes, simplify it.
- Change only what is necessary. Clean up only the problems that your changes cause.
- Do not improve adjacent code, comments or formatting. Do not refactor code that is not broken. Use the existing style.
- Prefer code that explains itself. Keep necessary comments short.
- If your changes make imports, variables or functions unused, remove them. If you see unrelated dead code, tell the human. Do not remove it unless the human asks.
- Each changed line must connect directly to the request.

## Writing

- Write in plain language, with Chicago Manual of Style mechanics.
- Use simple, precise words. Do not use em-dashes. Do not try to sound smart. Do not use complex management or executive language.
- Use the fewest words that keep the meaning and the accuracy.
- Use simple, correct formatting:
  - Keep paragraphs short.
  - Use lists for steps and options.
  - Use `code` for names, paths and commands.
  - Use bold only for a small number of key words.
  - Do not nest lists more than two levels.
- Write each link as short text that names the target. Use an HTML `<a>` element or a Markdown link, for example `[PR #123](url)`, `[WI 4567](url)` or `[design.md](url)`. Do not write a bare URL.
- For diagrams, follow the [diagram rules](https://github.com/microsoft/agentic-loops/blob/master/.github/skills/diagram.md).

### Teams chats

The Writing rules also apply.

- Put the main point or the request in the first line.
- Write about one topic in each message. For details, link to a document.
- Use lists. Do not use headings or tables.
- Use a diagram only if it is 60 columns wide or less. If it is wider, link to it.
- @mention a person only if that person must do an action.

## Sources

Design rules are adapted from [Anders](https://github.com/microsoft/agentic-loops/blob/53bcfc18147874f7434afb8aa71911b12f2940f7/.github/agents/anders.md#wip-mode); code rules from [Dave](https://github.com/microsoft/agentic-loops/blob/53bcfc18147874f7434afb8aa71911b12f2940f7/.github/agents/dave.md#roles--responsibilities).
The adapted material retains the [agentic-loops MIT license](LICENSE.agentic-loops).
