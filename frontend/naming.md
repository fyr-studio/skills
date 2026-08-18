# Frontend Naming Guidelines
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: remove product-specific and arbitrary file rules; align with core English-first naming
  - 1.0: initial version

Follow `../core/naming.md` first.

## General
- New frontend identifiers, filenames and comments are English unless an explicit legacy exception applies.
- Follow the language/framework's normal casing conventions.
- Name components/views for their role or domain meaning, not color/layout accidents.
- Event handlers should reveal the user/system action they handle.
- Boolean names should read as predicates where practical (`isLoading`, `hasAccess`, `canSubmit`).
- Types/contracts should describe meaning; avoid suffixes such as `Type`, `Data`, `Manager` when they add no clarity.

## Files
Use the dominant project convention for file casing and suffixes. A UI framework may have meaningful conventions (`Screen`, `Page`, `View`, `Controller`, `System`, etc.); do not impose a React-specific suffix on other platforms.

A file with one primary public type/component should normally be discoverable by that type/component name.

## Comments
Do not require decorative path comments at the top of every file. Comments should explain non-obvious intent, constraints or trade-offs.

## Checklist
- [ ] English-first new naming
- [ ] Ecosystem/project casing respected
- [ ] Names communicate intent
- [ ] Boolean/action names are readable
- [ ] No product-specific naming rule copied into unrelated projects
