# Backend Architecture Guidelines
version: 3.0
last-updated: 2026-08
changelog:
  - 3.0: generalize modular/clean architecture guidance across backend stacks
  - 2.0: modular monolith + Clean Architecture/CQRS guidance
  - 1.0: initial version

Follow `../core/architecture.md` and `../core/engineering-principles.md`.

## Default topology
Use one deployable modular backend until concrete operational evidence justifies extraction.

## Module layers
When useful, a business module may separate:
- Domain — entities/value objects/invariants/business rules;
- Application — use cases and ports/contracts;
- Infrastructure — database/provider/external I/O adapters;
- Presentation/Transport — HTTP/RPC/message handlers.

Names/folders may vary by stack. Do not create empty layers just to mimic the model.

Dependency direction is inward: business rules do not depend on frameworks, transports, databases or provider SDKs.

## Use-case orchestration
Prefer explicit use cases/handlers over god services that accumulate unrelated operations.

CQRS/mediator libraries are optional implementation patterns. If a project uses them, keep commands/queries at the application boundary and avoid turning the pattern into deep ceremony.

## Transport adapters
Controllers/routes/resolvers/message handlers should translate transport input to application use cases and translate results back. They should not own business invariants or repository/provider coordination.

## Cross-module access
Do not reach into another module's persistence/domain internals. Expose the smallest stable public capability/contract required by the consumer.

Do not create public facades speculatively for every module.

## Composition
Keep dependency registration/composition explicit and organized by module/feature where the framework supports it. Avoid a root bootstrap file that knows every implementation detail when modules can register themselves cleanly.

## Background work
Recurring/async workers belong to the module that owns the behavior. Separate scheduling/loop mechanics from one iteration/use case so it remains testable.

## Extraction
Consider another service/process only for concrete scaling, fault/security isolation, independent deployment, ownership or technology constraints that outweigh operational cost.

## Checklist
- [ ] One deployable remains default
- [ ] Business rules depend inward
- [ ] Transport adapters stay thin
- [ ] Use cases are explicit/cohesive
- [ ] Cross-module access uses minimal public contracts
- [ ] No CQRS/messaging/distribution added ceremonially
