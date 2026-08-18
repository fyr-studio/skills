# Architecture Principles — Core
version: 1.0
last-updated: 2026-08
changelog:
  - 1.0: initial cross-platform architecture standard

## Goal
Keep products understandable for one developer while preserving clean boundaries that allow growth without a structural rewrite.

## Separation of concerns
A unit should have one clear reason to change. Separate domain/business rules, application/use-case coordination, infrastructure/external I/O and presentation/transport concerns when those concerns exist.

Do not create empty ceremonial layers merely to satisfy a diagram.

## Dependency direction
Business rules must not depend on UI frameworks, HTTP transports, databases, SDKs or deployment providers.

Infrastructure depends inward on contracts owned by the code that needs the capability, not the reverse.

Prefer dependency inversion at boundaries that are volatile, external or meaningfully testable. Do not create interfaces for every class by habit.

## Module ownership
Business concepts belong to the module/domain that owns their meaning.

Avoid generic dumping grounds such as `Shared/Models`, `Common/DTOs`, `Helpers` or `Utils` for types with business meaning.

Cross-module access should use a minimal explicit public contract owned by the providing module rather than reaching into its internals.

## Composition over hidden coupling
Dependencies should be visible through constructors, composition roots, serialized contracts or explicit references appropriate to the platform.

Avoid service-locator patterns, global mutable state and hidden singleton dependencies unless the platform genuinely requires them and the cost is understood.

## Prefer cohesive duplication over premature abstraction
Do not abstract code solely because two fragments look similar. Abstract when they represent the same concept and are expected to evolve together.

Small duplication is cheaper than the wrong shared abstraction.

## Scale when evidence exists
A modular monolith / single application deployable is the default for server products unless evidence justifies distribution.

Extraction is justified by concrete needs such as materially different scaling, fault/security isolation, independent deployment cadence, ownership boundaries or incompatible technology constraints.

For clients/games, use the equivalent principle: keep one coherent application/runtime architecture until a real subsystem needs stronger isolation.

## Keep boundaries technology-neutral where useful
Core/application contracts should express business intent rather than provider/library terminology.

Provider-specific details belong at adapters/infrastructure boundaries.

## Checklist
- [ ] Responsibilities and ownership are clear
- [ ] Business rules do not depend on infrastructure/presentation
- [ ] No business types were placed in a generic dumping ground
- [ ] Cross-boundary dependencies use minimal explicit contracts
- [ ] No interface/abstraction was added without a concrete reason
- [ ] No distributed/complex topology was introduced speculatively
