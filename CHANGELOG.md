# Skills Changelog — Fyr Studio

Global change history for all skills.
For the specific changelog of each skill, see the file header.

## 2026-08

### Cross-project standards foundation
- Added `core/` as the authoritative home for reusable Fyr Studio engineering standards.
- Added `core/index.md` v1.0 orchestrator and explicit precedence: current-task decision > documented project exception/ADR > Fyr Studio standard > framework convention > incidental legacy code.
- Added `core/engineering-principles.md` v1.0: design before code, investigation before assumptions, scope discipline and no speculative complexity.
- Added `core/architecture.md` v1.0: reusable separation-of-concerns, dependency direction, module ownership and evidence-based scaling guidance.
- Added `core/naming.md` v1.0: English-first, intent-based, ecosystem-aware naming.
- Added `core/error-handling.md` v1.0: structured classification, safe boundaries, bounded retries and cancellation rules.
- Added `core/testing.md` v1.0: risk-based, behavior-focused, deterministic test strategy.
- Added `core/localization.md` v1.0: English is the canonical source/fallback; semantic keys; production locale completeness; bundled client core localization; locale-aware formatting; API machine-readable codes; server localization only for server-delivered human content.
- Reframed `README.md` so skills define Fyr Studio standards rather than product-specific implementation details.

### Backend architecture v2
- `backend/index.md` v2.0: project-specific decisions now override generic skills; backend defaults aligned with modular monolith + Clean Architecture.
- `backend/architecture.md` v2.0: removed legacy `Shared`, `BaseController`, Merchant validation and controller/repository business-logic guidance; added Clean Architecture per module, CQRS boundaries, module-owned DI, thin controllers and minimal `IPublic<Module>` cross-module facades.
- `backend/naming.md` v2.0: added CQRS/public-facade naming, modern .NET constant naming, and English-first guidance for new/reworked code.
- `backend/error-handling.md` v2.0: replaced controller-local/ad-hoc errors and `T()`/BaseController patterns with centralized exception handling, validation pipeline guidance and `ProblemDetails`.
- `backend/database.md` v2.0: repositories are module-owned persistence ports rather than one-per-table wrappers; added transaction/concurrency guidance and explicit project-owned schema source-of-truth policy.
- `backend/test-generator.md` v2.0: test guidance now centers domain/use-case/pipeline behavior instead of legacy controller/repository coupling.
- `backend/index.md` v1.1: added server-side localization skill.
- `backend/database.md` v1.1: required production schema changes to be reproducible and versioned.
- `backend/localization.md` v1.0: initial server-side localization guidance.

## 2026-06

### Initial release
- `frontend/index.md` v1.0: frontend orchestrator
- `frontend/naming.md` v1.0: naming conventions
- `frontend/architecture.md` v1.0: architecture and patterns
- `frontend/error-handling.md` v1.0: error handling
- `frontend/styling.md` v1.0: styles and UI
- `frontend/i18n.md` v1.0: internationalization
- `frontend/test-generator.md` v1.0: test generation
- `backend/index.md` v1.0: backend orchestrator
- `backend/naming.md` v1.0: naming conventions
- `backend/architecture.md` v1.0: architecture and patterns
- `backend/error-handling.md` v1.0: error handling
- `backend/database.md` v1.0: database patterns
- `backend/test-generator.md` v1.0: test generation
