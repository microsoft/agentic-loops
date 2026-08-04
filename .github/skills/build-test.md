---
name: build-test
description: Builds the project and runs fast tests (excludes integration tests). Used for fast feedback. Dave's done-done gate.
---

> **Template — preflight-gated.** Replace every `FILL_ME` placeholder with your stack's real commands.
> This is the *fast* gate Dave runs before declaring done-done; it must finish quickly and cleanly —
> no warnings, no errors. The loop will not start while any placeholder remains in this file.
> Delete this note block once the commands are filled in.

## Commands

### Frontend / UI (if applicable)

From `<<FILL_ME: web dir, or delete this section>>`

    <<FILL_ME: format command>>        # e.g. npm run format:fix
    <<FILL_ME: lint command>>          # e.g. npm run lint
    <<FILL_ME: unit test command>>     # e.g. npm run test:ci
    <<FILL_ME: build command>>         # e.g. npm run build

### Backend / core

From the project root

    <<FILL_ME: build command>>         # e.g. dotnet build <Solution> -c Release
    <<FILL_ME: unit test command>>     # e.g. dotnet test <Solution> -c Release --filter "type=UnitTests"
