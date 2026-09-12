---
name: yagni
description: Keep design and code simple, and communicate clearly in documents, comments, messages and diagrams.
---

# /yagni

For the rest of this session:

## Design

- Do not overdesign.
- Review at the codebase and product level for global consistency, integrity and optimization.
- Review against repository conventions. Apply Clean Architecture, YAGNI, DRY, SOLID and dependency-flow rules where applicable.
- Follow established patterns and conventions, but suggest more elegant, more DRY/SOLID, more performant or more secure designs when warranted. The human is the final decision-maker on any design change.
- Flag anything that is genuinely a product decision and hand it back to the human.

See also [agentic-loops meta-design](https://github.com/microsoft/agentic-loops/blob/master/docs/meta-design.md).

## Code

- Minimum code that solves the problem. Nothing speculative.
- No features beyond what was asked. No abstractions for single-use code.
- No flexibility or configurability that was not requested. No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.
- Ask: "Would a senior engineer say this is overcomplicated?" If yes, simplify.
- Touch only what you must. Clean up only your own mess.
- Do not improve adjacent code, comments or formatting. Do not refactor what is not broken. Match existing style.
- Prefer self-explanatory code. Keep necessary comments terse.
- Remove imports, variables and functions made unused by your changes. Mention unrelated dead code; do not remove it unless asked.
- Every changed line should trace directly to the request.

## Writing

- Write using simple and precise language. Avoid em-dashes. Don't try to sound smart. Don't use complex managerial or exec language.
- Use the principle of "least number of words to convey the meaning without losing accuracy".
- Put diagrams wherever a data flow or control flow is being discussed.
- Keep box text minimal. Fit each box to its text.
- Explain non-obvious boxes after the diagram.
- Put a short legend inside the diagram explaining box types and other notation.

### Diagram characters

Use Unicode box-drawing and block characters, restricted to this common subset:

| Style | Allowed code points |
|-------|---------------------|
| Single-line boxes | U+2500, U+2502, U+250C, U+2510, U+2514, U+2518, U+251C, U+2524, U+252C, U+2534, U+253C |
| Double-line boxes | U+2550, U+2551, U+2554, U+2557, U+255A, U+255D, U+2560, U+2563, U+2566, U+2569, U+256C |
| Solid and shaded blocks | U+2588, U+2591-U+2593 |

Ordinary ASCII text and arrows (`->`, `<-`, `^`, `v`) are also allowed. Avoid emoji, icons and other decorative glyphs.
Font support is not universal. Fall back to ASCII (`+`, `-`, `|`, `#`) when support for the palette is uncertain or rendering is broken.

## Sources

Design rules are adapted from [Anders](https://github.com/microsoft/agentic-loops/blob/53bcfc18147874f7434afb8aa71911b12f2940f7/.github/agents/anders.md#wip-mode); code rules from [Dave](https://github.com/microsoft/agentic-loops/blob/53bcfc18147874f7434afb8aa71911b12f2940f7/.github/agents/dave.md#roles--responsibilities).
The adapted material retains the [agentic-loops MIT license](LICENSE.agentic-loops).
