# Test Generator — Backend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When generating, modifying or reviewing tests for
backend code.

## Why
Tests catch regressions in business logic, validate that
repositories generate correct SQL, and document the
expected behavior of each endpoint.

## Stack
- xUnit
- Moq (mocking)
- FluentAssertions (readable assertions)

### Setup if not configured
```bash
dotnet add package xunit
dotnet add package Moq
dotnet add package FluentAssertions
dotnet add package Microsoft.AspNetCore.Mvc.Testing
```

## Folder structure
```
YourProject.Tests/
├── Controllers/
│   ├── DebtControllerTests.cs
│   └── MerchantControllerTests.cs
├── Repositories/
│   ├── DebtRepositoryTests.cs
│   └── DebtPaymentRepositoryTests.cs
├── Services/
│   ├── GeminiServiceTests.cs
│   └── VietQrServiceTests.cs
└── Utils/
    └── HelperTests.cs
```

## Priority

**High — test these first:**
- Controllers — endpoint behavior, HTTP status codes, validation
- Services — business logic (Gemini processing, VietQR generation)

**Medium:**
- Repositories — SQL query correctness (integration tests with test DB)

**Do not test:**
- DTOs (no logic)
- Models (no logic)
- Program.cs (infrastructure)

## Templates

### Controller test template
```csharp
// Controllers/DebtControllerTests.cs
public class DebtControllerTests {
    private readonly Mock<IDebtRepository> _debtRepoMock;
    private readonly Mock<IDebtPaymentRepository> _paymentRepoMock;
    private readonly DebtController _controller;

    public DebtControllerTests() {
        _debtRepoMock = new Mock<IDebtRepository>();
        _paymentRepoMock = new Mock<IDebtPaymentRepository>();
        _controller = new DebtController(_debtRepoMock.Object, _paymentRepoMock.Object);
    }

    [Fact]
    public async Task PartialPayment_ReturnsOk_WhenAmountIsValid() {
        // Arrange
        var debtId = Guid.NewGuid();
        var debt = new Debt { Id = debtId, Amount = 500000, PaidAmount = 0, Status = "pending" };
        _debtRepoMock.Setup(r => r.GetDebtByIdAsync(debtId)).ReturnsAsync(debt);

        var request = new PartialPaymentRequestDto { Amount = 200000 };

        // Act
        var result = await _controller.PartialPayment(debtId, request, "user-id");

        // Assert
        result.Should().BeOfType<OkObjectResult>();
    }

    [Fact]
    public async Task PartialPayment_ReturnsBadRequest_WhenAmountExceedsBalance() {
        // Arrange
        var debtId = Guid.NewGuid();
        var debt = new Debt { Id = debtId, Amount = 500000, PaidAmount = 400000 };
        _debtRepoMock.Setup(r => r.GetDebtByIdAsync(debtId)).ReturnsAsync(debt);

        var request = new PartialPaymentRequestDto { Amount = 200000 }; // exceeds 100000 balance

        // Act
        var result = await _controller.PartialPayment(debtId, request, "user-id");

        // Assert
        result.Should().BeOfType<BadRequestObjectResult>();
    }

    [Fact]
    public async Task PartialPayment_ReturnsNotFound_WhenDebtDoesNotExist() {
        // Arrange
        _debtRepoMock.Setup(r => r.GetDebtByIdAsync(It.IsAny<Guid>())).ReturnsAsync((Debt?)null);

        // Act
        var result = await _controller.PartialPayment(Guid.NewGuid(), new PartialPaymentRequestDto { Amount = 100 }, "user-id");

        // Assert
        result.Should().BeOfType<NotFoundObjectResult>();
    }
}
```

### Service test template
```csharp
// Services/VietQrServiceTests.cs
public class VietQrServiceTests {
    private readonly VietQrService _service;

    public VietQrServiceTests() {
        _service = new VietQrService();
    }

    [Fact]
    public void GenerateQrUrl_ReturnsCorrectUrl_WithValidInputs() {
        // Arrange
        var bankBin = "970418";
        var accountNumber = "1234567890";
        var amount = 500000L;
        var description = "Payment";

        // Act
        var url = _service.GenerateQrUrl(bankBin, accountNumber, amount, description);

        // Assert
        url.Should().Contain(bankBin);
        url.Should().Contain(accountNumber);
        url.Should().Contain("500000");
    }

    [Theory]
    [InlineData("", "1234567890", 500000)]
    [InlineData("970418", "", 500000)]
    [InlineData("970418", "1234567890", 0)]
    public void GenerateQrUrl_ReturnsNull_WhenInputsAreInvalid(
        string bankBin, string account, long amount) {
        var url = _service.GenerateQrUrl(bankBin, account, amount, "desc");
        url.Should().BeNull();
    }
}
```

### Repository integration test template
```csharp
// Repositories/DebtRepositoryTests.cs
// Note: requires a test database connection
public class DebtRepositoryTests : IDisposable {
    private readonly NpgsqlConnection _db;
    private readonly DebtRepository _repository;

    public DebtRepositoryTests() {
        _db = new NpgsqlConnection(TestConnectionString);
        _repository = new DebtRepository(_db);
    }

    [Fact]
    public async Task GetDebtByIdAsync_ReturnsDebt_WhenExists() {
        // Arrange — insert test data
        var debtId = Guid.NewGuid();
        await _db.ExecuteAsync(
            "INSERT INTO debts (id, amount) VALUES (@Id, @Amount)",
            new { Id = debtId, Amount = 500000L });

        // Act
        var debt = await _repository.GetDebtByIdAsync(debtId);

        // Assert
        debt.Should().NotBeNull();
        debt!.Amount.Should().Be(500000L);
    }

    public void Dispose() {
        // Clean up test data
        _db.Execute("DELETE FROM debts WHERE id = @Id", new { Id = testDebtId });
        _db.Dispose();
    }
}
```

## Naming conventions

```csharp
// Pattern: MethodName_ExpectedResult_WhenCondition
[Fact]
public async Task PartialPayment_ReturnsOk_WhenAmountIsValid() { }

[Fact]
public async Task PartialPayment_ReturnsBadRequest_WhenAmountIsZero() { }

[Fact]
public async Task GetDebtById_ReturnsNull_WhenDebtDoesNotExist() { }
```

## Checklist
- [ ] Tests are in a separate `*.Tests` project
- [ ] Controllers tested with mocked repositories
- [ ] Services tested with mocked external dependencies
- [ ] Each test has a single clear assertion
- [ ] Test names follow MethodName_Result_Condition pattern
- [ ] Integration tests clean up after themselves
- [ ] No tests for DTOs or Models

## Meta — Evolution
If a new testing pattern is needed for a specific feature →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The feature and why existing templates don't cover it
- The proposed test pattern
- Whether it's an extension or correction
