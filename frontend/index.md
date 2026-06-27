# Frontend Skills — Orchestrator
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
Any task that involves writing, modifying or reviewing
frontend code in Fyr Studio projects.

## Why
This file acts as the single entry point for all frontend
skills. The AI reads it first and decides which skills to
load based on the task, avoiding loading unnecessary context.

## How to use

Based on the task, read the corresponding skill before
writing any code:

| Task | Skill |
|---|---|
| Naming variables, functions, components, files | `naming.md` |
| Defining architecture, patterns, folder structure | `architecture.md` |
| Handling errors, catches, error messages | `error-handling.md` |
| Styles, colors, UI, visual components | `styling.md` |
| Texts, translations, languages | `i18n.md` |
| Generating or modifying tests | `test-generator.md` |

For tasks involving multiple areas, read all relevant
skills before writing code.

## Current stack
- React Native + Expo (TypeScript)
- React Navigation (Stack)
- i18next
- AsyncStorage
- Supabase Auth

> ⚠️ Legacy exception: Debt Assistant (frontend & backend) uses
> Spanish for variables and comments. All new projects follow
> English conventions as defined in naming.md.

⚠️ If the stack changes, update this file and the affected
skills with the corresponding change type (Breaking if it
affects core conventions).

## Global checklist
Before delivering any frontend code verify:
- [ ] All relevant skills were read for the task
- [ ] Code follows naming.md conventions
- [ ] Architecture follows architecture.md
- [ ] Errors follow error-handling.md
- [ ] Texts use i18n according to i18n.md
- [ ] Styles follow styling.md

## Meta — Evolution
If a task does not fit any existing skill →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- What new area needs its own skill
- Whether it's an extension, correction or breaking change
- Suggested version
