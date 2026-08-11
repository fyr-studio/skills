# Database Guidelines — Backend
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: align Dapper/Npgsql access with Clean Architecture, explicit transaction boundaries, project-owned schema policy and repository ownership
  - 1.1: require production schema changes to be reproducible and versioned
  - 1.0: initial version

## When to use this skill
When writing SQL, repositories, database session/transaction infrastructure,
Dapper mappings, schema changes, or PostgreSQL persistence code.

## Why
Database access should stay explicit and auditable without leaking PostgreSQL or
Npgsql concerns into Application/Domain code. Correct transaction boundaries and
project-owned schema conventions matter more than imposing one persistence pattern
on every project.

## Default stack
- PostgreSQL
- Dapper over Npgsql
- Raw SQL, unless the project explicitly chooses another persistence stack

Always follow the repository's documented stack and schema-management policy.

## SQL rules

### Explicit SELECT columns and aliases
Avoid `SELECT *` in application queries.

Alias snake_case database columns to the C# property names expected by Dapper.

```sql
SELECT
    id          AS Id,
    business_id AS BusinessId,
    created_at  AS CreatedAt
FROM debts
WHERE id = @DebtId;
```

### Parameterize all values
Never concatenate untrusted or runtime values into SQL.

```csharp
await connection.QuerySingleOrDefaultAsync<DebtRecord>(
    sql,
    new { DebtId = debtId },
    transaction: transaction);
```

Identifiers that cannot be parameterized (for example a dynamic ORDER BY column)
must come from an explicit allow-list, never raw client input.

### Choose the query API that matches cardinality
Use APIs whose semantics match the expected result:
- `QuerySingleAsync` when exactly one row must exist;
- `QuerySingleOrDefaultAsync` when zero or one row is valid;
- `QueryAsync` for collections;
- `ExecuteAsync` for non-query commands.

Do not allocate a collection just to call `FirstOrDefault()` for a single-row
lookup.

### PostgreSQL types
Use Npgsql-supported CLR mappings appropriate to the installed Npgsql version.
Do not copy historical conversion workarounds without verifying that they are
still required by the current package/version and target column type.

Be explicit about UTC vs local business dates:
- instants/events should normally be stored as UTC timestamps;
- local calendar dates/times that are business rules may intentionally use
  PostgreSQL `date`/`time` types;
- do not convert a local-business rule to UTC merely because UTC is the default
  for timestamps.

## Repository ownership
Repository interfaces belong to the owning module's Application layer.
Implementations belong to that module's Infrastructure layer.

```text
Modules/Debts/
  Application/
    IDebtRepository.cs
  Infrastructure/
    DebtRepository.cs
```

Do not place repository interfaces in a global Shared/Common contracts folder.

Do not use generic repositories (`IRepository<T>`, `IBaseRepository`) unless a
project has a proven, explicit need.

### Repository granularity
Do not enforce "exactly one repository per table".

A repository is an application/domain persistence port, not a table wrapper.
Its shape should follow cohesive use cases and module ownership while keeping SQL
inside Infrastructure.

A repository may query several tables when that is necessary to materialize the
concept it owns. Conversely, one large table may justify multiple focused query
components when that produces clearer responsibilities.

Repositories must not contain domain decision-making solely because the data is
available there.

## Clean Architecture boundary
Application and Domain code should not directly depend on:
- `NpgsqlConnection`;
- `NpgsqlTransaction`;
- Dapper static extension methods;
- PostgreSQL-specific exception types unless translated at the infrastructure
  boundary.

Infrastructure owns those details.

For projects needing shared transaction participation, a small project-owned
database-session abstraction may live in a cross-cutting database area.
Avoid a large generic UnitOfWork abstraction unless it solves a concrete problem.

## Transactions
Use a transaction when several writes must succeed or fail atomically.

When the project uses CQRS, prefer transaction management at the application
pipeline/use-case boundary for commands explicitly marked as transactional.

Do not automatically wrap every command in a database transaction.
External calls (AI, push, payment providers, HTTP APIs) can make a blanket
transaction dangerously long-lived.

Conceptually:

```text
Transactional command
  -> transaction behavior
  -> begin
  -> handler
  -> repositories share current session/transaction
  -> commit

failure -> rollback -> rethrow
```

Do not open an independent transaction inside each repository when one application
operation must be atomic across repositories.

## Concurrency and correctness
When correctness depends on current database state, use PostgreSQL concurrency
controls deliberately:
- unique constraints;
- foreign keys;
- check constraints;
- row locks where appropriate;
- atomic update/insert statements;
- idempotency constraints/keys;
- transaction isolation appropriate to the invariant.

Do not rely on a prior `SELECT` followed by an unprotected write when concurrent
requests can invalidate the decision.

## Delete semantics
Do not impose a universal "never hard delete" rule.

Use the deletion semantics defined by the domain and schema:
- cancellation/state transition when history must be retained;
- archive when records remain addressable;
- purge/hard delete when the product explicitly defines permanent removal.

Never substitute soft-delete flags for a product requirement without design
approval.

## Schema source of truth
Every project must define where its authoritative schema lives.

Follow that project-specific policy exactly.

Possible valid policies include:
- migrations committed with the backend;
- a dedicated database repository;
- one living schema file maintained outside the application repository;
- an infrastructure project/tooling directory.

Do not create a new migration framework or duplicate schema files merely because
this generic skill prefers reproducibility.

The non-negotiable requirement is that the project has one explicit source of
truth and changes are reproducible from it.

If SQL is applied manually:
1. update the project's authoritative schema/migration source as required by its
   documented workflow;
2. distinguish planned SQL from already-applied SQL;
3. never claim an environment was changed unless it actually was;
4. do not create a second competing schema source.

If repository guidance conflicts with this generic skill, the explicit
project-specific schema policy wins.

## Error handling
Translate provider/database-specific failures at the Infrastructure/Application
boundary when they have meaningful application semantics.

Example: a known unique-constraint race may become a `ConflictException`.

Do not expose raw PostgreSQL/Npgsql exception messages to HTTP clients.

## Checklist
- [ ] SELECTs use explicit columns and Dapper aliases
- [ ] Runtime values are parameterized
- [ ] Query APIs match expected cardinality
- [ ] Repository interfaces live in the owning module's Application layer
- [ ] Repository implementations/SQL live in Infrastructure
- [ ] No generic repository/table-wrapper pattern was added without need
- [ ] Application/Domain do not depend directly on Npgsql/Dapper infrastructure
- [ ] Multi-write invariants use explicit transaction/concurrency protection
- [ ] External I/O is not accidentally held inside blanket DB transactions
- [ ] Delete behavior follows the domain rather than a universal soft-delete rule
- [ ] Schema changes update the project's one authoritative source of truth
- [ ] No duplicate schema source was created

## Meta — Evolution
If a new database pattern is needed →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- the pattern and problem it solves;
- whether it is an extension, correction or breaking change.
