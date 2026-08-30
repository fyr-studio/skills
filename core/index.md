# Core Skills — Orchestrator
version: 1.1.0
last-updated: 2026-08
changelog:
  - 1.1.0: add git workflow and independent verification skills
  - 1.0.0: initial Fyr Studio cross-project engineering standards

## When to use
Read this orchestrator for any non-trivial coding, architecture, review or refactor task in a Fyr Studio project.

## Purpose
Core skills define standards that must stay consistent across platforms and products. Frontend/backend skills may adapt implementation details but must not redefine these principles.

## Precedence

```text
explicit user decision for the current task
  > explicit documented project exception / ADR / product constraint
  > Fyr Studio skills
  > framework/platform convention
  > incidental legacy code
```

Do not treat surrounding legacy code as a reason to reproduce a known weaker pattern.

## Load by concern

| Concern | Skill |
|---|---|
| Design process, scope, trade-offs, implementation discipline | `engineering-principles.md` |
| Boundaries, dependencies, simplicity vs scale | `architecture.md` |
| Branching, commits, parallel work, PR handoff | `git-workflow.md` |
| Identifier language and naming intent | `naming.md` |
| Failures, recovery, user-safe errors | `error-handling.md` |
| Test strategy and priorities | `testing.md` |
| Independent acceptance-criteria verification | `verification.md` |
| Languages, locale, formatting, translation contracts | `localization.md` |

Read every relevant core skill before implementation. `engineering-principles.md` is relevant to every non-trivial implementation task. Load `git-workflow.md` whenever repository/git operations are part of delivery. Load `verification.md` only when independent verification is explicitly requested or required.

## Global non-negotiables
- Design decisions come before implementation when a real ambiguity exists.
- Investigate relevant repository state before relying on assumptions.
- New code identifiers/comments are English unless a documented legacy exception applies.
- Prefer simple architecture with clear boundaries over speculative complexity.
- Do not silently change unrelated systems while implementing a scoped task.
- Explicit no-touch boundaries must be preserved.
- User-facing behavior must fail safely and predictably.
- English is the canonical localization source language and fallback.
- Product/platform constraints may justify an explicit exception; incidental legacy code does not.
- Report only validation that was actually executed or observed; blocked checks stay blocked.

## Meta — Evolution
Report **[SKILL UPDATE SUGGESTED]** only for guidance that should apply across Fyr Studio projects, not for product-specific architecture or domain rules.
