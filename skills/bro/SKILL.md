---
name: bro
description: "Re-explain the previous assistant message in a much simpler way, for when the reply made you go 'bro what'. Use /bro to get a simpler version of the last answer in Plain Language + Chicago Manual of Style, with diagrams where they help."
license: MIT
---

# /bro: say it simpler

The user typed `/bro`. Your last message was not clear to the user. It was too dense, had too much jargon, or was too formal.

**Your task:** explain YOUR most recent assistant message again in a much simpler way.

## Rules

1. **Explain again. Do not answer again.** Never answer a new question. Never add new information. Never use tools. Exception: you can read the diagram rules in rule 5. Only say again what you already said, in a different way.
2. **Simpler, not necessarily shorter.** If the idea needs space to be clear, use the space. The goal is "impossible to misunderstand", not "fewer words". Remove preamble, hedging and consultant-speak. Keep the length that real clarity needs.
3. **Keep the facts exactly.** Each path, command, filename, number, URL, name and decision stays EXACTLY the same. Make the explanation around the facts simpler. Never change the facts.
4. **Use Plain Language + Chicago Manual of Style.** Do not use em-dashes.
5. **Add diagrams where they help.** If the idea is a data flow or a control flow, add a diagram. Follow the [diagram rules](https://github.com/microsoft/agentic-loops/blob/master/.github/skills/diagram.md).
6. **Same language.** If your original message was not in English, write the simpler version in that language, with the same short, simple sentences.
7. **Flatten the structure.** Remove headers and ceremony. Change tables into plain sentences or a diagram. Keep a short list only if the original really had more than one part.
8. **Edge case:** if this conversation has no previous assistant message, say that there is nothing to simplify yet.
