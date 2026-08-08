# Database Guidelines — Backend
version: 1.1
last-updated: 2026-08
changelog:
  - 1.1: require production schema changes to be reproducible and versioned in source control
  - 1.0: initial version

## When to use this skill
When writing SQL queries, creating repositories,
defining database models or working with Dapper and Npgsql.

## Why
Consistent database access patterns prevent bugs from
type mismatches, make queries readable and auditable,
and ensure Dapper maps results correctly every time.

## Stack
- PostgreSQL via Supabase (Singapore region)
- Dapper over Npgsql
- No ORM — raw SQL only

## Rules

### Always use explicit column aliases
Dapper maps by name. Always alias columns to match
the C# property name exactly (PascalCase).

✅
```sql
SELECT
    id            AS Id,
    merchant_id   AS MerchantId,
    amount        AS Amount,
    paid_amount   AS PaidAmount,
    due_date      AS DueDate,
    created_at    AS CreatedAt
FROM debts
WHERE id = @DebtId
```

❌
```sql
SELECT * FROM debts WHERE id = @DebtId
-- Dapper may fail to map snake_case columns to PascalCase properties
```

### Always use parameterized queries
NEVER concatenate user input into SQL strings.

✅
```csharp
await _db.QueryAsync<Debt>(
    "SELECT id AS Id FROM debts WHERE merchant_id = @MerchantId",
    new { MerchantId = merchantId });
```

❌
```csharp
// SQL injection vulnerability — NEVER do this
await _db.QueryAsync<Debt>(
    $"SELECT id AS Id FROM debts WHERE merchant_id = '{merchantId}'");
```

### DateOnly → DateTime? conversion for PostgreSQL
PostgreSQL doesn't accept `DateOnly` directly via Npgsql.
Always convert before passing as parameter.

✅
```csharp
DateTime? dueDate = request.DueDate.HasValue
    ? request.DueDate.Value.ToDateTime(TimeOnly.MinValue)
    : null;

await _db.ExecuteAsync(
    "INSERT INTO debts (due_date) VALUES (@DueDate)",
    new { DueDate = dueDate });
```

❌
```csharp
// Throws runtime exception with Npgsql
await _db.ExecuteAsync(
    "INSERT INTO debts (due_date) VALUES (@DueDate)",
    new { DueDate = request.DueDate }); // DateOnly not supported
```

### Use QuerySingleOrDefaultAsync for single row lookups
Never use `QueryAsync` and then `.FirstOrDefault()`.

✅
```csharp
var debt = await _db.QuerySingleOrDefaultAsync<Debt>(
    "SELECT id AS Id FROM debts WHERE id = @Id",
    new { Id = debtId });
```

❌
```csharp
var debts = await _db.QueryAsync<Debt>(
    "SELECT id AS Id FROM debts WHERE id = @Id",
    new { Id = debtId });
var debt = debts.FirstOrDefault(); // unnecessary allocation
```

### Soft deletes — never hard delete
Records are never physically deleted.
Always use status flags or soft delete columns.

✅
```csharp
await _db.ExecuteAsync(
    "UPDATE debts SET status = 'cancelled' WHERE id = @DebtId",
    new { DebtId = debtId });
```

❌
```csharp
await _db.ExecuteAsync(
    "DELETE FROM debts WHERE id = @DebtId",
    new { DebtId = debtId });
```

### Repository pattern — one repository per table
Each table has exactly one repository.
Repositories contain SQL only — no business logic.

✅
```csharp
// Modules/DebtManagement/DebtRepository.cs
public class DebtRepository : IDebtRepository {
    public async Task<Debt?> GetDebtByIdAsync(Guid debtId) {
        return await _db.QuerySingleOrDefaultAsync<Debt>(
            "SELECT id AS Id, amount AS Amount FROM debts WHERE id = @Id",
            new { Id = debtId });
    }
}
```

❌ Business logic in repository:
```csharp
public async Task<Debt?> GetDebtByIdAsync(Guid debtId) {
    var debt = await _db.QuerySingleOrDefaultAsync<Debt>(...);
    // WRONG — business logic belongs in controller
    if (debt != null && debt.PaidAmount >= debt.Amount) {
        await MarkAsPaidAsync(debtId);
    }
    return debt;
}
```

### Timestamps — always use UTC
Store all timestamps in UTC. Convert to Vietnam time (UTC+7)
only when displaying to the user.

✅
```csharp
CreatedAt = DateTime.UtcNow,
PaidAt = DateTime.UtcNow,
```

❌
```csharp
CreatedAt = DateTime.Now, // local time — unpredictable in Railway
```

### Use transactions for multi-step writes
When multiple tables must be updated atomically, use a transaction.

✅
```csharp
using var transaction = _db.BeginTransaction();
try {
    await _db.ExecuteAsync("INSERT INTO debt_payments ...", payment, transaction);
    await _db.ExecuteAsync("UPDATE debts SET paid_amount = ...", new { ... }, transaction);
    transaction.Commit();
} catch {
    transaction.Rollback();
    throw;
}
```

### Production schema changes must be reproducible and versioned
The live database must never be the only source of truth for structural schema changes.

Any structural change applied to production, staging, or another shared environment must be represented in source control, even when the project does not use a formal migration framework.

This includes, when applicable:
- tables and columns
- primary/foreign keys and other constraints
- indexes
- triggers and functions
- RLS configuration/policies
- grants or other schema-level permissions

If the project already has a migration/schema convention, follow it.

If it does not, do **not** introduce a large migration framework just for one change. Prefer small, ordered SQL files in a clearly documented database/migrations or database/scripts location so another developer or AI agent can reconstruct how the schema reached its current state.

When SQL is applied manually through Supabase or another database console:
1. Commit the equivalent SQL to the repository as part of the same change.
2. Keep it safe/idempotent where practical (`IF EXISTS`, `IF NOT EXISTS`, guarded constraints, etc.).
3. Record enough context to distinguish an already-applied production change from SQL that is still pending.
4. Never claim a migration was executed against an environment unless it was actually executed.

Before concluding that a table, column, constraint, policy, trigger, or function does not exist, inspect both the repository's versioned database definition and the real target database when access is available.

❌ Never rely on an undocumented manual production change:
```text
Run SQL in Supabase → change works → SQL is never committed
```

✅ Keep the change reproducible:
```text
Create/update versioned SQL in repo → review → apply manually if required →
record/report that production was applied
```

## Checklist
- [ ] All SELECT queries use explicit AS aliases in PascalCase
- [ ] No string concatenation in SQL — always use @Parameters
- [ ] DateOnly converted to DateTime? before Npgsql parameters
- [ ] Single row lookups use QuerySingleOrDefaultAsync
- [ ] No hard deletes — always soft delete
- [ ] No business logic in repositories
- [ ] All timestamps use DateTime.UtcNow
- [ ] Multi-step writes use transactions
- [ ] Structural database changes are reproducible and versioned in source control
- [ ] Manual/shared-environment SQL is not left undocumented outside the repository

## Meta — Evolution
If a new database pattern is needed →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The pattern and the problem it solves
- Whether it's an extension or correction
