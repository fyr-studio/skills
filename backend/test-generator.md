# Test Generator — Backend
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: align testing with modular Clean Architecture, CQRS, pipeline behaviors and thin controllers
  - 1.0: initial version

## When to use this skill
When generating, modifying or reviewing backend tests.

## Why
Tests should protect business behavior, application boundaries, transaction and
authorization rules, and integration points without coupling the suite to
implementation details.

## Default stack
- xUnit
- Moq
- FluentAssertions
- Microsoft.NET.Test.Sdk
- Microsoft.AspNetCore.Mvc.Testing when end-to-end HTTP integration tests are
  actually useful

Respect versions already pinned by the project.

## Structure
Mirror the production concepts rather than organizing everything around
controllers.

Example:

```text
DebtAssistant.Api.Tests/
  Common/
    Authentication/
    Cqrs/
    Web/
  Modules/
    Businesses/
      Application/
    Debts/
      Domain/
      Application/
      Infrastructure/
```

Do not create empty test folders for modules that do not yet exist.

## Priority

### High priority
Test behavior with meaningful failure risk:
- domain invariants and calculations;
- command/query handlers with branching business rules;
- FluentValidation validators and validation pipeline behavior;
- authorization/business-scope behaviors;
- transaction commit/rollback behavior;
- idempotency and concurrency-sensitive behavior where practical;
- exception-to-ProblemDetails/status mapping;
- security-sensitive identity extraction/authorization logic.

### Medium priority
- repositories with non-trivial SQL or mappings, preferably with integration tests
  against an isolated test database when feasible;
- external-provider adapters with deterministic mocked HTTP boundaries;
- background-worker iteration logic separated from the infinite scheduling loop.

### Usually low value
Do not test trivial:
- property-only records/DTOs;
- DI boilerplate solely to increase coverage;
- framework behavior already guaranteed by ASP.NET/MediatR;
- implementation details such as private helper invocation order.

## CQRS tests
Test handlers as application use cases.

Example:

```csharp
[Fact]
public async Task Handle_ThrowsConflict_WhenPaymentExceedsRemainingBalance()
{
    var repository = new Mock<IDebtRepository>();
    // arrange current persisted state

    var handler = new RegisterDebtPaymentCommandHandler(repository.Object);

    var act = () => handler.Handle(command, CancellationToken.None);

    await act.Should().ThrowAsync<ConflictException>();
}
```

Do not test the same business rule again through a controller unless the HTTP
mapping itself is what matters.

## Validation tests
For FluentValidation, test meaningful rules directly and test the shared
`ValidationBehavior` once as infrastructure.

Avoid duplicating every validator test at both validator and HTTP levels.

## Pipeline behavior tests
Shared behaviors deserve focused tests because they affect many modules.

Examples:
- `ValidationBehavior` does not call the handler on validation failure;
- `TransactionBehavior` commits on success;
- `TransactionBehavior` rolls back and rethrows on failure;
- business authorization behavior rejects invalid scope before invoking the
  handler.

## Error handling tests
Test central mappings rather than duplicating controller error assertions.

Verify known exceptions map to the intended status and unexpected exceptions do
not expose internal details.

## Controller tests
Controllers should be thin. Unit-test them only when they contain meaningful HTTP
translation behavior that is not already covered elsewhere.

Do not mock repositories directly into controllers in an architecture where
controllers only depend on `ISender`.

If testing a thin controller is worthwhile, mock/send through the application
boundary rather than bypassing it with internal repositories.

## Repository tests
Prefer integration tests for SQL correctness when the query itself is the thing
being validated.

Use an isolated disposable/test database strategy appropriate to the project.
Tests must not mutate shared production/staging data.

Cover important cases such as:
- Dapper aliases/materialization;
- unique/conflict behavior;
- transaction participation;
- row locking/concurrency where correctness depends on it.

Do not write brittle tests that assert large SQL strings character-for-character
unless exact SQL text is itself contractual.

## External provider tests
Mock the network boundary, not the entire application.

Test:
- request construction when non-trivial;
- response parsing;
- transient failure/retry classification;
- cancellation;
- provider-specific error translation.

Never use live API keys in unit tests.

## Background services
Separate one logical iteration from the scheduling loop where practical, then test
the iteration behavior directly.

Do not write tests that sleep for real-world intervals.

## Authentication tests
Do not depend on a live identity/JWKS endpoint for unit tests.

Test project-owned code such as:
- current-user claim extraction;
- configuration derivation/validation;
- business authorization behavior.

Let framework integration tests cover JWT middleware only when necessary, using
local deterministic signing keys rather than production secrets.

## Naming
Use descriptive behavior-oriented test names.

Recommended pattern:

```text
MethodOrUseCase_ExpectedBehavior_WhenCondition
```

Examples:

```csharp
Handle_ThrowsForbidden_WhenBusinessIsNotOwnedByAccount()
Handle_CommitsTransaction_WhenCommandSucceeds()
TryHandleAsync_Returns404_WhenResourceIsMissing()
```

## Assertions
Prefer the smallest set of assertions necessary to prove the behavior.

"One assertion per test" is not a strict rule. Multiple closely related
assertions are acceptable when they collectively verify one behavior.

Avoid tests whose only purpose is improving coverage percentage.

## Checklist
- [ ] Tests mirror modules/application concepts rather than legacy controller structure
- [ ] Domain and handler business rules are tested where they live
- [ ] Shared pipeline behaviors have focused tests
- [ ] Central error mapping is tested centrally
- [ ] Thin controllers are not over-tested
- [ ] Repository SQL tests use isolated integration infrastructure when needed
- [ ] No test hits production/staging data or live external APIs
- [ ] Auth unit tests do not require live JWKS/network access
- [ ] Test names describe behavior and condition
- [ ] Assertions protect behavior rather than implementation details

## Meta — Evolution
If a new testing pattern is needed →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- the feature and why existing guidance does not cover it;
- the proposed pattern;
- whether it is an extension, correction or breaking change.
