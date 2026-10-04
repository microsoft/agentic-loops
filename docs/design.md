# Design

> `<<FILL_ME: replace this file with the project's current design; delete all placeholder text>>`

Keep this file current and short. Put planned work in `docs/backlog.md`.

## Repository operations

_Trunk fallback, generated and acquired artifacts, CI/CD, and who owns startup and preflight._

_The chosen workflow, branch pattern, work-record path, and working-root convention.
For worktrees, include the existing helper or the native Git procedure._

<!-- OPTIONAL:LIVENESS:BEGIN -->
_The local run, restart, and liveness mechanism._
<!-- OPTIONAL:LIVENESS:END -->

## System overview

_What the system is, who uses it, and its core domains._

## Architecture

_Layers, boundaries, and dependency-flow rules (for example, Clean Architecture)._

## Key components

_The major modules and services, and what each one is responsible for._

## Dependency direction

_Allowed dependencies and important runtime flows._

## Build and verification

_The standard local and CI entry points, test boundaries, artifacts, and required evidence._

## Cross-cutting concerns

_Authentication, persistence, configuration and secrets, observability, and error handling._

- Use OpenTelemetry only for telemetry.

## Conventions

_Stack, coding standards, compatibility targets, and language-specific rules._

## Agent constraints

_Preferred form of address, project-specific scope, compatibility, bootstrap ownership, and
verification requirements. Keep generic role governance in `.github/agents/`._
