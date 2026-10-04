---
applyTo: "**/*.cs"
---

# .NET conventions

- Use the least-privilege access modifier possible.
- Never use `internal`.
- Use EnsureThat for argument validation.
- Do not test the guard behavior of EnsureThat. Test the application behavior or the integration.
- For logging, use only the source-generated `[LoggerMessage]` logging of
  Microsoft.Extensions.Logging 6+.
- Keep one root partial logging class for each top-level component.
