# Frontend Skills — Orchestrator
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: remove product/framework-specific assumptions and align frontend guidance with core standards
  - 1.0: initial version

## When to use
Use for any task that designs, writes, modifies or reviews user-facing client/UI code.

## Required core skills
Read `../core/index.md` first, then load every relevant core skill.

## Frontend skills
| Concern | Skill |
|---|---|
| Client/UI boundaries, state, services, navigation | `architecture.md` |
| Frontend identifiers/files/components | `naming.md` |
| User-visible and async error behavior | `error-handling.md` |
| Visual consistency/design-system implementation | `styling.md` |
| UI localization implementation | `i18n.md` |
| Frontend testing | `test-generator.md` |

## Rules
- Follow the project's chosen platform/framework; do not assume React, Expo, Unity, web or a state library from this repository alone.
- Apply core standards first; this layer adapts them to client/UI concerns.
- Prefer platform-native capabilities when they satisfy the requirement cleanly.
- Do not introduce a new state, navigation, styling or networking framework without a design reason.
- Preserve explicit project constraints and documented exceptions.

## Checklist
- [ ] Core orchestrator and relevant core skills read
- [ ] Relevant frontend skills read
- [ ] No framework/product assumptions invented
- [ ] UI behavior, localization and errors follow Fyr Studio standards

## Meta — Evolution
Use **[SKILL UPDATE SUGGESTED]** only for reusable frontend guidance, never for product-specific screens or domain rules.
