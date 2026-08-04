---
name: build-test-full
description: Full build + complete test suite including integration tests, plus every lint/quality gate. Bhaskar's done-done gate.
---

> **Template — preflight-gated.** Replace every `FILL_ME` placeholder with your stack's real commands.
> This is the *full* gate Bhaskar runs; it must pass completely — no warnings, no errors — including
> integration tests and every project-defined lint/quality gate. The loop will not start while any
> placeholder remains in this file.
> Delete this note block once the commands are filled in.

## Commands

### Frontend / UI (if applicable)

From `<<FILL_ME: web dir, or delete this section>>`

    <<FILL_ME: format check command>>
    <<FILL_ME: lint command>>
    <<FILL_ME: full test command>>     # unit + component/integration
    <<FILL_ME: build command>>

### Backend / core

From the project root

    <<FILL_ME: build command>>         # e.g. dotnet build <Solution> -c Release
    <<FILL_ME: full test command>>     # unit + integration

### Lint / quality gates (run on EVERY change)

    <<FILL_ME: lint gate(s), or "none">>
