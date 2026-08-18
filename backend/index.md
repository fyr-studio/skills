# Backend Skills — Orchestrator
version: 3.0
last-updated: 2026-08
changelog:
  - 3.0: align backend standards with cross-project core guidance and remove mandatory .NET/PostgreSQL stack assumptions
  - 2.0: modular Clean Architecture/CQRS guidance
  - 1.0: initial version

## When to use
Use for any task that designs, writes, modifies or reviews server/backend code.

## Required core skills
Read `../core/index.md` first and all relevant core skills.

## Backend skills
| Concern | Skill |
|---|---|
| Server modules/boundaries/use cases | `architecture.md` |
| Backend naming | `naming.md` |
| API/server failures | `error-handling.md` |
| Relational persistence/schema/transactions | `database.md` |
| Server-rendered localization | `localization.md` |
| Backend tests | `test-generator.md` |

## Default direction
- Prefer a modular monolith / one deployable until evidence justifies distribution.
- Keep domain/application logic independent from transport, persistence and provider SDKs.
- Keep HTTP/API adapters thin.
- Use explicit module boundaries and minimal public contracts.
- Choose framework, mediator, ORM/query library, database and hosting provider per project requirements; do not infer them from this generic skill.
- Do not introduce CQRS, mediator libraries, event buses or repositories ceremonially. Use them where the project has adopted them or the design justifies them.

## Checklist
- [ ] Core standards loaded
- [ ] Relevant backend skills loaded
- [ ] Project stack inspected rather than assumed
- [ ] No incidental legacy architecture copied into new modules
- [ ] No speculative distributed complexity added

## Meta — Evolution
Report **[SKILL UPDATE SUGGESTED]** only for reusable backend engineering guidance.
