# Architecture Guidelines — Backend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When creating new modules, endpoints, services or deciding
where to put business logic in the backend.

## Why
A consistent modular monolith architecture is the right
choice for MVP-stage products. It's faster to develop than
microservices, easier to deploy, and can be extracted into
services when real scale justifies it.

## Rules

### Modular monolith — no microservices at MVP stage
Do not split into microservices until a module has
demonstrably different scaling needs or a separate team owns it.

✅ Current architecture:
```
Modules/
├── DebtManagement/     ← debt business logic
├── AudioProcessing/    ← Gemini, entity extraction
├── Payments/           ← VietQR
├── Notifications/      ← push, subscription alerts
└── Tenancy/            ← merchants, subscriptions
Shared/
├── DTOs/               ← data transfer objects only
├── Models/             ← domain models
└── Contracts/          ← interfaces only
```

❌ Premature extraction:
```
// Don't create a separate AudioProcessing microservice
// until recording volume justifies independent scaling
```

### Shared folder rules
- `Shared/DTOs/` — data transfer objects only, no logic
- `Shared/Contracts/` — interfaces only, no implementations
- `Shared/Models/` — domain models only, no business logic

✅
```csharp
// Shared/Contracts/IDebtRepository.cs — interface only
public interface IDebtRepository {
    Task<Debt?> GetDebtByIdAsync(Guid debtId);
}

// Shared/DTOs/PartialPaymentRequestDto.cs — data only
public class PartialPaymentRequestDto {
    public long Amount { get; set; }
    public string? Note { get; set; }
}
```

❌
```csharp
// Shared/DTOs/DebtDto.cs — no logic in DTOs
public class DebtDto {
    public long GetRemainingBalance() => Amount - PaidAmount; // WRONG
}
```

### BaseController for merchant validation
All controllers that require an authenticated merchant
must extend `BaseController` and use `ValidateMerchantAsync`.

✅
```csharp
public class DebtController : BaseController {
    [HttpPost]
    public async Task<IActionResult> CreateDebt(
        [FromBody] CreateDebtRequestDto request,
        [FromQuery] string UserId)
    {
        var (merchant, error) = await ValidateMerchantAsync(UserId);
        if (error != null) return error;
        // ... rest of logic
    }
}
```

❌
```csharp
// Never duplicate merchant validation logic in each endpoint
public async Task<IActionResult> CreateDebt(...) {
    var merchant = await _merchantRepo.GetByUserIdAsync(UserId);
    if (merchant == null) return Unauthorized();
    if (merchant.ExpiresAt < DateTime.UtcNow) return Unauthorized();
    // ... this is already in BaseController
}
```

### Layer responsibilities
```
Controller  → validate input, call repository/service, return HTTP response
Repository  → SQL queries only, no business logic
Service     → cross-cutting business logic (Gemini, VietQR, Push)
```

✅ Controller calls repository:
```csharp
// Controller
var debt = await _debtRepository.GetDebtByIdAsync(debtId);
if (debt == null) return NotFound(...);

// Repository
public async Task<Debt?> GetDebtByIdAsync(Guid debtId) {
    return await _db.QuerySingleOrDefaultAsync<Debt>(
        "SELECT * FROM debts WHERE id = @DebtId", new { DebtId = debtId });
}
```

❌ Business logic in repository:
```csharp
// Repository — WRONG
public async Task<Debt?> GetDebtByIdAsync(Guid debtId) {
    var debt = await _db.QuerySingleOrDefaultAsync<Debt>(...);
    if (debt.PaidAmount >= debt.Amount) debt.Status = "paid"; // business logic!
    return debt;
}
```

### Background services
Use `BackgroundService` for recurring tasks (cron jobs).
Register in `Program.cs` with `AddHostedService`.

✅
```csharp
// Modules/Notifications/DebtReminderService.cs
public class DebtReminderService : BackgroundService {
    protected override async Task ExecuteAsync(CancellationToken ct) { ... }
}

// Program.cs
builder.Services.AddHostedService<DebtReminderService>();
```

### When to extract a module to microservice
Only when ALL of these are true:
1. The module has 10x more traffic than others
2. A separate team owns it
3. Deployment cycles need to be independent

## Checklist
- [ ] No microservices created without justified reason
- [ ] Shared/DTOs has no logic
- [ ] Shared/Contracts has only interfaces
- [ ] All authenticated endpoints use BaseController
- [ ] Business logic is in Controller or Service, not Repository
- [ ] Background services registered with AddHostedService

## Meta — Evolution
If a new architectural pattern is needed →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The pattern and the problem it solves
- Why existing architecture doesn't cover it
- Whether it's an extension or breaking change
