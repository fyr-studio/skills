# Naming Guidelines — Backend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When naming classes, interfaces, methods, variables,
files, DTOs or any identifier in backend code.

## Why
Consistent naming across the codebase makes it predictable,
reduces the time to find things and aligns with .NET
community conventions.

> ⚠️ Legacy exception: Debt Assistant uses Spanish for comments.
> All new Fyr Studio projects use English for everything.

## Rules

### Classes and interfaces
PascalCase. Interfaces prefixed with `I`.

✅
```csharp
public class DebtRepository { }
public interface IDebtRepository { }
public class PartialPaymentRequestDto { }
```

❌
```csharp
public class debtRepository { }
public interface DebtRepository { } ← missing I prefix
public class partialPaymentRequestDto { }
```

### Methods
PascalCase. Async methods suffixed with `Async`.

✅
```csharp
public async Task<Debt> GetDebtByIdAsync(Guid debtId) { }
public async Task RegisterPaymentAsync(Guid debtId, long amount) { }
public IActionResult ValidateMerchant(string userId) { }
```

❌
```csharp
public async Task<Debt> getDebtById(Guid debtId) { } ← camelCase
public async Task RegisterPayment(Guid debtId) { } ← missing Async suffix
```

### Variables and parameters
camelCase in English.

✅
```csharp
var merchantId = Guid.NewGuid();
var remainingBalance = debt.Amount - debt.PaidAmount;
string userId = request.UserId;
```

❌
```csharp
var MerchantId = Guid.NewGuid(); ← PascalCase
var saldo_restante = debt.Amount - debt.PaidAmount; ← snake_case + Spanish
```

### Constants and static readonly
SCREAMING_SNAKE_CASE.

✅
```csharp
private const int MAX_RETRY_ATTEMPTS = 3;
private static readonly TimeSpan NOTIFICATION_HOUR = new TimeSpan(1, 0, 0);
```

### DTOs
PascalCase + `Dto` suffix. Request DTOs + `RequestDto`.

✅
```csharp
public class PartialPaymentRequestDto { }
public class DebtPaymentDto { }
public class MerchantRegisteredDto { }
```

❌
```csharp
public class PartialPaymentRequest { } ← missing Dto suffix
public class PaymentDTO { } ← uppercase DTO
```

### Controllers
PascalCase + `Controller` suffix. One controller per domain.

✅
```csharp
public class DebtController : BaseController { }
public class MerchantController : BaseController { }
```

### Repositories
PascalCase + `Repository` suffix. Interface prefixed with `I`.

✅
```csharp
public interface IDebtRepository { }
public class DebtRepository : IDebtRepository { }
```

### Files
Match the class name exactly.

✅
```
DebtController.cs
IDebtRepository.cs
DebtRepository.cs
PartialPaymentRequestDto.cs
```

### Database columns → C# properties
Map using Dapper aliases. DB uses snake_case, C# uses PascalCase.

✅
```csharp
// DB column: paid_amount → C# property: PaidAmount
SELECT paid_amount AS PaidAmount FROM debts
```

### Comments
Always in English. Describe why, not what.

✅
```csharp
// Exponential backoff: 2s, 5s, 10s before giving up
await Task.Delay(retryDelay);
```

❌
```csharp
// Wait before retry ← describes the obvious
await Task.Delay(retryDelay);
```

## Checklist
- [ ] Classes and interfaces in PascalCase
- [ ] Interfaces prefixed with I
- [ ] Async methods suffixed with Async
- [ ] Variables in camelCase English
- [ ] DTOs suffixed with Dto
- [ ] Request DTOs suffixed with RequestDto
- [ ] DB columns mapped with AS PascalCase alias
- [ ] Comments in English describing why not what

## Meta — Evolution
If a naming case not covered here emerges →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The case and suggested convention
- Whether it's an extension or correction
