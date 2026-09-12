# Feature: .NET governance
**Branch:** vibe/006-dotnet-governance
**Status:** Complete

## Requirements

- Rename C# guidance to .NET guidance.
- Require Microsoft.Extensions.Logging 6+ source-generated logging.
- Keep one root partial logging class per top-level component.
- Require OpenTelemetry for telemetry.
- Require simple, precise English.

## Decision

Keep cross-project rules in shared governance and stack rules in path-scoped instructions.

## Tasks

| # | Task | Status |
|---|------|--------|
| T1 | Rename and extend .NET instructions | Complete |
| T2 | Add global telemetry and writing rules | Complete |
