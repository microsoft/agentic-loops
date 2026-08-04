---
name: agentify
description: Install this governance framework into a target project, or update an existing project's agent-loop artifacts; then stamp the adopted framework version (agentify commit hash) and report any remaining FILL_ME placeholders.
---

The distribution / versioning mechanism for this framework. Run from a checkout of **agentify** with
the target repo path in hand. Copilot-only scope. Do this on a `vibe/<nnn>-adopt-agentify` branch in the
target — never on its trunk — so the human reviews the diff before merge.

## Ownership model

- **Framework-owned** (refresh on update): `AGENTS.md`, `.github/agents/`, `.github/skills/` **except the
  consumer-filled `build-test.md` / `build-test-full.md`**, `docs/meta-design.md`,
  `docs/features/TASK_FILE_TEMPLATE.md`, and the **rules** portion of
  `.github/copilot-instructions.md` (everything *above* the `## Project profile` heading).
- **Consumer-owned** (create only if absent, never clobber): `docs/design.md`, `docs/backlog.md`,
  `docs/features/<nnn>-*.md`, the **Project profile** block of `.github/copilot-instructions.md`, and the
  build/test skills `.github/skills/build-test.md` / `.github/skills/build-test-full.md` (their command
  bodies are the consumer's — never overwrite once filled).
- **Plumbing** (`.editorconfig`, `.gitignore`, `.gitattributes`, `.vscode/`): create only if absent.

## What it does

1. **Copy / update artifacts** into the target per the ownership model above.
2. **Stamp the version.** Record the current agentify commit hash into the target's
   `.github/copilot-instructions.md` → Project profile → *Framework version adopted*:

       git -C <agentify-dir> rev-parse HEAD

   On update, replace the previously stamped hash with the new one (leave other profile values intact).
3. **Report remaining placeholders.** Run the target's `.github/skills/preflight.md` placeholder scan
   and list every unfilled `<<FILL_ME:` placeholder the human must complete before the loop can start.

## Install (new target) · PowerShell sketch

> **New target only.** The recursive copy below overwrites `.github/` wholesale (including the Project
> profile). For a repo that already adopted the framework, use **Update** below — never re-run Install.

    $src = "<agentify-dir>"; $dst = "<target-repo>"
    Copy-Item "$src/AGENTS.md","$src/.editorconfig","$src/.gitignore","$src/.gitattributes" $dst
    Copy-Item "$src/.github" $dst -Recurse -Force
    Copy-Item "$src/.vscode" $dst -Recurse
    New-Item "$dst/docs/features" -ItemType Directory -Force
    Copy-Item "$src/docs/meta-design.md" "$dst/docs"
    Copy-Item "$src/docs/features/TASK_FILE_TEMPLATE.md" "$dst/docs/features"
    if (-not (Test-Path "$dst/docs/design.md"))  { Copy-Item "$src/docs/design.md"  "$dst/docs" }
    if (-not (Test-Path "$dst/docs/backlog.md")) { Copy-Item "$src/docs/backlog.md" "$dst/docs" }
    # then stamp the hash (step 2) and run preflight (step 3)

## Update (existing target)

Refresh framework-owned files only; **preserve** the Project profile block, `docs/design.md`, and the
filled `build-test.md` / `build-test-full.md` command bodies — plus all other consumer-owned files;
re-stamp the hash; re-run preflight. If the rules portion of `copilot-instructions.md` changed, splice in
the new rules while keeping the consumer's existing `## Project profile` section verbatim.
