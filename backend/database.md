# Database Guidelines — Backend
version: 3.0
last-updated: 2026-08
changelog:
  - 3.0: generalize relational persistence guidance beyond PostgreSQL/Dapper while preserving correctness standards
  - 2.0: Clean Architecture transaction/concurrency/schema guidance
  - 1.0: initial version

Follow `../core/architecture.md` and `../core/testing.md`.

## Project stack first
Inspect and follow the database, ORM/query library and schema-management workflow chosen by the project. Do not introduce another persistence stack merely because a skill example used it.

## Persistence boundaries
Domain/application code should depend on persistence contracts/capabilities, not concrete database connections, ORM sessions or provider exceptions when that coupling can reasonably be isolated.

Persistence contracts belong to the module/use case that owns the concept, not a global generic repository folder.

Do not mandate one repository per table or a generic `Repository<T>` pattern.

## Queries
- Select only needed fields when the query technology makes that practical.
- Parameterize runtime values; never concatenate untrusted input into query text.
- Use APIs whose cardinality/semantics match the expected result.
- Keep business decision-making out of low-level data-access code.

## Transactions
Use a transaction for operations whose writes must succeed/fail atomically.

Choose the transaction boundary at the application/use-case level so participating persistence operations share it.

Do not hold database transactions open across slow external calls unless the design explicitly requires it and the trade-off is understood.

## Concurrency
Protect invariants with the database's available correctness mechanisms: unique/foreign/check constraints, optimistic concurrency/versioning, atomic updates, locks, idempotency keys and suitable isolation.

Do not rely on unprotected read-then-write sequences when concurrent requests can invalidate the decision.

## Time
Store instants in an unambiguous form (normally UTC) while preserving local calendar dates/times when the business rule itself is local. Do not convert a local-calendar rule into an instant without reason.

## Delete semantics
Hard delete, archive, soft delete or state transition is a domain/product decision. Do not impose one universal deletion policy.

## Schema source of truth
Every project must have exactly one explicit authoritative schema/migration source and a reproducible process for applying changes. Do not create a competing source of truth.

## Error translation
Translate database/provider errors when they represent stable application semantics (for example known uniqueness/concurrency conflict). Never expose raw database diagnostics to clients.

## Checklist
- [ ] Project persistence stack followed
- [ ] Domain/application isolated from unnecessary provider details
- [ ] Runtime query values parameterized
- [ ] Transaction boundary matches the invariant
- [ ] External I/O not accidentally inside long transactions
- [ ] Concurrency-sensitive invariants protected atomically
- [ ] One authoritative reproducible schema source exists
