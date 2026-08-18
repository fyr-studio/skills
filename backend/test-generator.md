# Backend Testing Guidelines
version: 3.0
last-updated: 2026-08
changelog:
  - 3.0: remove .NET-specific tooling assumptions and align with core risk-based testing
  - 2.0: modular Clean Architecture/CQRS testing guidance
  - 1.0: initial version

Follow `../core/testing.md`.

## Use existing stack
Use the project's established test framework, mocking/fake tools and integration-test infrastructure. Do not introduce xUnit/Jest/Moq/etc. solely because another project uses it.

## High priority
- domain invariants/calculations;
- branching application use cases;
- authorization/tenant scope;
- transaction/concurrency/idempotency behavior;
- central error-to-transport mapping;
- security-sensitive identity/permission logic;
- non-trivial persistence mappings/queries;
- external provider adapters/parsing/retry classification.

## Application use cases
Test use-case behavior at the application boundary. Do not retest the same rule through a controller/route unless transport mapping itself is contractual.

## Persistence
When query/mapping/constraint behavior matters, prefer isolated integration tests against an appropriate disposable/test database rather than mocking SQL/ORM internals.

Never mutate shared production/staging data.

## Providers
Mock/fake the network/provider boundary, not the whole application. Never use production API keys in tests.

## Background work
Separate one logical iteration/use case from scheduling loops where practical; test the iteration without real-time sleeps.

## Transport adapters
Thin endpoints usually need fewer unit tests. Focus tests on transport-specific mapping/serialization/authorization behavior not already covered below.

## Checklist
- [ ] Existing project test stack reused
- [ ] Highest-risk backend behavior covered first
- [ ] Business rules tested at their owning layer
- [ ] Database correctness uses isolated integration tests when valuable
- [ ] No production/staging data or live provider secrets
- [ ] Thin transport adapters are not over-tested
