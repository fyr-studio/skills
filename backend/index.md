# Backend Skills — Orchestrator
version: 1.1
last-updated: 2026-08
changelog:
  - 1.1: add server-side localization skill
  - 1.0: initial version

## When to use this skill
Any task that involves writing, modifying or reviewing
backend code in Fyr Studio projects.

## Why
This file acts as the single entry point for all backend
skills. The AI reads it first and decides which skills to
load based on the task, avoiding loading unnecessary context.

## How to use

Based on the task, read the corresponding skill before
writing any code:

| Task | Skill |
|---|---|
| Naming classes, methods, variables, files | `naming.md` |
| Defining architecture, modules, folder structure | `architecture.md` |
| Handling errors, exceptions, HTTP responses | `error-handling.md` |
| Database queries, repositories, Dapper patterns | `database.md` |
| Server-side localization, locale packs, localized notifications/messages | `localization.md` |
| Generating or modifying tests | `test-generator.md` |

For tasks involving multiple areas, read all relevant
skills before writing code.

## Current stack
- .NET 8 Web API
- Dapper + Npgsql (PostgreSQL)
- Supabase (PostgreSQL — Singapore region)
- Railway (deployment — Asia region)

⚠️ If the stack changes, update this file and the affected
skills with the corresponding change type (Breaking if it
affects core conventions).

## Global checklist
Before delivering any backend code verify:
- [ ] All relevant skills were read for the task
- [ ] Code follows naming.md conventions
- [ ] Architecture follows architecture.md
- [ ] Errors follow error-handling.md
- [ ] DB queries follow database.md
- [ ] Server-localized content follows localization.md when applicable

## Meta — Evolution
If a task does not fit any existing skill →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- What new area needs its own skill
- Whether it's an extension, correction or breaking change
- Suggested version
