# Naming Guidelines — Backend
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: align naming with modular Clean Architecture, CQRS, public module facades and English-first new code
  - 1.0: initial version

## When to use this skill
When naming classes, interfaces, methods, variables, files, commands, queries,
DTOs/contracts or any backend identifier.

## Why
Consistent naming keeps the codebase predictable, makes architectural intent
visible and reduces ambiguity for both humans and coding agents.

Project-specific legacy exceptions may exist. Preserve an explicit project
exception when documented, but new and substantially rewritten code should follow
the current project conventions.

## Language
New backend code uses English for:
- class/interface/record names;
- methods;
- variables and parameters;
- comments;
- namespaces and filenames.

Legacy files may retain historical comments when they are not being rewritten.
Do not spend a refactor translating unrelated comments solely for cosmetic
consistency.

## Rules

### Classes, records and interfaces
Use PascalCase. Interfaces use the `I` prefix.

```csharp
public sealed class DebtRepository { }
public interface IDebtRepository { }
public sealed record DebtSummary(...);
```

### Methods
Use PascalCase. Async methods end in `Async`.

```csharp
Task<Debt?> GetDebtByIdAsync(Guid debtId, CancellationToken cancellationToken);
Task RegisterPaymentAsync(...);
```

Do not append `Async` to synchronous methods.

### Variables and parameters
Use camelCase English names.

```csharp
var businessId = request.BusinessId;
var remainingBalance = debt.CalculateRemainingBalance();
```

Avoid Hungarian notation, type prefixes and abbreviated names that hide meaning.

### Constants
Prefer PascalCase for C# constants and static readonly members, following modern
.NET conventions.

```csharp
private const int MaxRetryAttempts = 3;
private static readonly TimeSpan DefaultTimeout = TimeSpan.FromSeconds(30);
```

Do not introduce SCREAMING_SNAKE_CASE in new C# code unless an existing project
explicitly standardizes it.

### Commands and queries
Name application requests by intent.

```text
CreateDebtCommand
RegisterDebtPaymentCommand
GetDebtByIdQuery
ListContactsQuery
```

Handlers use the corresponding suffix:

```text
CreateDebtCommandHandler
GetDebtByIdQueryHandler
```

Validators follow the request:

```text
CreateDebtCommandValidator
```

Avoid vague names such as:

```text
DebtAction
DebtProcessor
DebtManager
HandleDebtService
```

### Application contracts
Repository interfaces should describe the domain concept they persist:

```text
IDebtRepository
IContactRepository
```

Do not create generic repository names such as:

```text
IRepository<T>
IGenericRepository
IBaseRepository
```

### Cross-module public facades
When another module needs a stable public capability, the target module may
expose one root-level facade using:

```text
IPublic<ModuleConcept>
```

Use singular module concept names:

```text
IPublicContact
IPublicBusiness
IPublicBilling
IPublicConfiguration
```

Do not name the facade after an implementation detail such as:

```text
IContactRepositoryPublic
IContactInternalService
```

Small immutable records used only by that facade should use names describing the
consumer-visible concept, e.g.:

```text
ContactReference
PaymentConfigurationReference
```

Do not expose Domain entities merely to avoid creating a small contract record.

### Controllers
Use PascalCase + `Controller`.

```text
DebtController
ContactController
ConfigurationController
```

Controllers normally inherit directly from `ControllerBase`.
Do not encode shared domain/authentication behavior into names or a mandatory
custom base controller.

### DTOs vs application contracts
Use `Dto` when a type is genuinely a transport/data-transfer object and the
project uses that convention.

HTTP request/response transport types may use:

```text
CreateDebtRequestDto
DebtResponseDto
```

Application Commands/Queries are not DTOs and should not carry the `Dto` suffix.

Public module contract records also do not require the `Dto` suffix unless they
are specifically transport DTOs.

### Files
A file containing one primary public type should match that type exactly.

```text
CreateDebtCommand.cs
CreateDebtCommandHandler.cs
IDebtRepository.cs
DebtRepository.cs
IPublicContact.cs
DependencyInjection.cs
```

For a root public facade file, small contract records may live beside the
interface until growth justifies separating them.

### Database columns -> C# properties
PostgreSQL uses snake_case. C# uses PascalCase.

With Dapper, explicitly alias selected columns:

```sql
SELECT
    business_id AS BusinessId,
    created_at  AS CreatedAt
FROM debts;
```

### Comments
Comments in newly written code are English and explain why, constraints or
non-obvious decisions—not the syntax immediately below them.

Good:

```csharp
// Reserve the idempotency key before sending to prevent duplicate deliveries.
```

Weak:

```csharp
// Insert row.
```

## Checklist
- [ ] Classes/interfaces/records use PascalCase
- [ ] Interfaces use `I`
- [ ] Async methods use `Async`
- [ ] Variables and parameters use camelCase English
- [ ] Commands/Queries/Handlers/Validators reveal use-case intent
- [ ] No generic repository/base-service naming was introduced
- [ ] Cross-module facades use singular `IPublic<ModuleConcept>` names
- [ ] New C# constants follow PascalCase unless the project says otherwise
- [ ] Application requests are not mislabeled as DTOs
- [ ] DB columns are explicitly aliased to PascalCase properties
- [ ] New comments are English and explain non-obvious intent

## Meta — Evolution
If a naming case is not covered →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- the case and suggested convention;
- whether it is an extension or correction.
