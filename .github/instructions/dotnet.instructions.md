---
applyTo: "**/*.cs"
---

# .NET conventions

- Prefer the least-privilege access modifier.
- Never use `internal`.
- Use EnsureThat for argument validation.
- Do not test EnsureThat's guard behavior; test application behavior or integration.
- Use only Microsoft.Extensions.Logging 6+ source-generated `[LoggerMessage]` logging.
- Keep one root partial logging class per top-level component.
