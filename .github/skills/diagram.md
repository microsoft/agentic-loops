# Diagram rules

- When the text is about a data flow or a control flow, add a diagram.
- Keep the text in boxes to a minimum. Make each box fit its text.
- After the diagram, explain each box that is not obvious.
- In the diagram, put a short legend that explains the box types and other notation.
- Put diagrams in fenced `text` blocks to keep the monospaced layout.

## Characters

Use standard Unicode characters that common modern OS and browser fonts support:

- Use the full Box Drawing (U+2500-U+257F) and Block Elements (U+2580-U+259F) ranges. This includes
  light, heavy, double, rounded and dashed lines, and solid, fractional and shaded blocks.
- You can use common arrows and technical symbols when they help.
- Do not use obscure glyphs, private-use characters, custom-font icons, emoji or decorative
  combining-character sequences.

Font support is not always the same. Use an ASCII fallback for known ASCII-only targets or for real
rendering problems. Do not use it only because the font is not known.
