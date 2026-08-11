# Backend Skills — Orchestrator
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: align orchestrator with modular Clean Architecture/CQRS backend guidance and explicit project-specific overrides
  - 1.1: add server-side localization skill
  - 1.0: initial version

## When to use this skill
Any task that writes, modifies, reviews or designs backend code in Fyr Studio
projects.

## Why
This file is the entry point for backend skills. Read it first, then load only
the skills relevant to the task.

## Precedence
These skills define Fyr Studio defaults, not project-specific product decisions.

When instructions conflict, use this precedence:

```text
explicit user decision for the current task
  > repository project instructions (AGENTS.md / CLAUDE.md / project docs)
  > relevant Fyr Studio skill
  > generic framework convention
```

Do not silently use a generic skill to undo an explicit project architecture or
schema workflow.

## How to use
Read the relevant skills before writing code:

| Task | Skill |
|---|---|
| Naming classes, methods, variables, files, commands/queries | `naming.md` |
| Architecture, modules, Clean Architecture, CQRS boundaries, DI | `architecture.md` |
| Errors, validation failures, exceptions, ProblemDetails | `error-handling.md` |
| SQL, Dapper/Npgsql, repositories, transactions, schema workflow | `database.md` |
| Server-owned localization, locale packs, localized messages | `localization.md` |
| Tests and test architecture | `test-generator.md` |

For work spanning multiple areas, read all relevant skills before implementation.

## Default backend direction
Unless a project explicitly says otherwise:
- .NET 8+ Web API
- modular monolith before microservices
- Clean Architecture inside business modules
- CQRS when the project has adopted it; do not introduce it silently
- PostgreSQL with Dapper/Npgsql when using the Fyr Studio relational stack
- centralized HTTP error handling
- explicit module boundaries
- one deployable backend unless extraction is justified by evidence

Exact providers, deployment regions, authentication systems, package versions and
schema locations are project-specific. Read the repository before assuming them.

## Global checklist
Before delivering backend changes verify:
- [ ] Repository/project instructions were read first
- [ ] All relevant backend skills were read
- [ ] Naming follows `naming.md`
- [ ] Architecture follows `architecture.md` plus project-specific decisions
- [ ] Errors follow `error-handling.md`
- [ ] DB work follows `database.md` and the project's authoritative schema policy
- [ ] Localization follows `localization.md` when backend-owned localized content is involved
- [ ] Tests follow `test-generator.md` when tests are added/modified
- [ ] No generic skill contradicted an explicit project decision

## Meta — Evolution
If a task exposes missing or incorrect reusable guidance, report:

**[SKILL UPDATE SUGGESTED]**

Include:
- the missing/incorrect area;
- whether it is an extension, correction or breaking change;
- suggested semantic version impact.
