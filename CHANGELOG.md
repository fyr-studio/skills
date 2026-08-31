# Skills Changelog — Fyr Studio

Global change history for all skills.
For the specific changelog of each skill, see the file header.

## 2026-08

### Shared agent workflow v1.1
- Added `core/git-workflow.md` v1.0.0 with the default task-branch/commit/push/PR workflow, parallel-branch synchronization guidance, git safety rules, and required branch/commit/push/final-status reporting.
- Added `core/verification.md` v1.0.0 as the agent-agnostic acceptance-criteria verification standard with PASS/FAIL/BLOCKED outcomes and strict separation of manual verification.
- Updated `core/engineering-principles.md` to v1.1.0 so final implementation reports include actual validation commands/results, blocked validation, manual wiring, scope boundaries and git metadata when applicable.
- Updated `core/index.md` to v1.1.0 to orchestrate the new git-workflow and verification skills.
- Reduced `agents/claude/fyr-verification.md` to a Claude-specific wrapper over the shared core verification policy.
- Replaced the per-project Git-submodule integration guidance with a single shared local checkout, canonically `C:\Proyectos\skills`, referenced directly by each project's `AGENTS.md`.

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
- Updated `core/testing.md` to v1.1 with deterministic retry-policy testing guidance, including contractual delays/classification and controlled time.

### Frontend standards v2
- `frontend/index.md` v2.0 now loads core standards and no longer declares React Native/Expo/i18next/Supabase as the Fyr Studio frontend stack.
- `frontend/architecture.md` v2.0 replaces fixed React Native folders, Context/AsyncStorage/React Navigation rules and `services/api.ts` assumptions with platform-neutral client boundaries, scoped state and explicit external adapters.
- `frontend/naming.md` v2.0 removes Debt Assistant exceptions, mandatory `Screen` suffixes and decorative file-path comments; naming is English-first and ecosystem-aware.
- `frontend/error-handling.md` v2.0 removes `mostrarError`, duplicate-debt/share-sheet special cases and defines reusable client error classification/presentation rules.
- `frontend/styling.md` v2.0 removes a fixed product color palette, `StyleSheet.create`, FlatList and React Native-specific mechanics; products own design tokens while Fyr Studio standardizes consistency/accessibility.
- `frontend/i18n.md` v2.0 adopts the core localization standard: English canonical fallback, semantic keys, bundled client core localization, locale-aware formatting and language-neutral API codes.
- `frontend/test-generator.md` v2.0 removes Expo/Jest/React Native/Debt Assistant templates and uses the project's established test stack with risk-based priorities.

### Backend standards v3
- `backend/index.md` v3.0 now loads core standards and removes mandatory .NET/PostgreSQL/Dapper assumptions.
- `backend/architecture.md` v3.0 preserves modular monolith/Clean Architecture intent while making CQRS/mediators and concrete folder names optional implementation choices.
- `backend/naming.md` v3.0 generalizes intent-based naming across backend languages.
- `backend/error-handling.md` v3.0 keeps centralized structured error semantics without requiring ASP.NET/MediatR specifically.
- `backend/database.md` v3.0 preserves transaction/concurrency/schema correctness while removing PostgreSQL/Dapper as universal requirements.
- `backend/localization.md` v2.0 limits server localization to server-delivered human content and aligns API/client responsibilities with the core standard.
- `backend/test-generator.md` v3.0 removes xUnit/Moq/.NET assumptions while preserving behavior- and risk-focused testing guidance.
- Updated `backend/database.md` to v3.1 with explicit claim/lease-before-external-I/O guidance and durable retry-state requirements for background processors.

### Backend architecture v2 (historical)
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
