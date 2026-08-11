# Error Handling Guidelines — Backend
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: replace controller-local HTTP error handling and BaseController localization with centralized exceptions, FluentValidation-compatible validation, ProblemDetails and framework auth responses
  - 1.0: initial version

## When to use this skill
When defining validation failures, domain/application exceptions, HTTP error
mapping, retry behavior, background worker error handling, or external-provider
failure handling.

## Why
A predictable error strategy keeps business/application code independent of HTTP,
prevents duplicated try/catch blocks, gives clients consistent responses and
avoids leaking internal details.

Project-specific mappings may refine this skill. Do not silently change an
explicitly documented API contract.

## Core flow

For ASP.NET backends using CQRS, prefer:

```text
HTTP
  -> Controller
  -> MediatR pipeline / validation
  -> Handler / Domain
  -> exception
  -> ASP.NET IExceptionHandler
  -> ProblemDetails
```

Controllers should not translate every expected failure themselves.
Application and Domain code must not return `IActionResult`, HTTP status codes or
ASP.NET `ProblemDetails`.

## Validation
Use a request-validation layer such as FluentValidation when the project has
standardized on it.

Input validation belongs before the handler where practical:

```text
Command/Query
  -> ValidationBehavior
  -> Validator(s)
  -> Handler
```

Examples of input validation:
- required fields;
- positive numeric input;
- string length/format;
- syntactic IDs and request shape.

Do not use request validation to replace domain invariants or state-dependent
business rules.

Domain/application logic still owns rules such as:
- payment exceeds current balance;
- operation cannot be cancelled in its current state;
- subscription does not allow an action;
- inventory transition violates a domain invariant.

## Generic cross-cutting exceptions
A backend may define a small set of genuinely generic exception categories in a
cross-cutting errors area, for example:

```text
NotFoundException
ForbiddenException
ConflictException
BusinessRuleException
ValidationException
```

Do not put every module-specific exception into Common merely because the global
handler can map it.

A module-specific error remains in its owning module and is still processed by
the same centralized pipeline.

## HTTP mapping
Use the project's central `IExceptionHandler` (or equivalent HTTP boundary) to
map known failures to `ProblemDetails`.

Default guidance:

| Situation | Status |
|---|---|
| Request validation failure | 400 Bad Request |
| Authentication missing/invalid | 401 Unauthorized |
| Authenticated but not permitted | 403 Forbidden |
| Resource not found | 404 Not Found |
| State/version/duplicate conflict | 409 Conflict |
| Business rule conflicting with current state | 409 Conflict unless the project defines otherwise |
| Unexpected server failure | 500 Internal Server Error |

Authentication failures produced by framework JWT/auth middleware should remain
authentication concerns. Do not turn them into domain `ForbiddenException`s.

## ProblemDetails
Prefer standardized `ProblemDetails` responses rather than ad-hoc anonymous
objects with an `error` field.

Validation responses should preserve useful structured field-level errors when
available.

Do not leak:
- stack traces;
- exception type names unnecessarily;
- SQL text/schema details;
- connection strings;
- API keys;
- bearer tokens;
- raw provider error bodies containing secrets;
- internal file paths.

Unexpected 500 responses must use a generic client-facing message while logs retain
sufficient diagnostic context.

## Controllers
Do not wrap controller actions in generic try/catch blocks.

Good:

```csharp
[HttpPost]
public async Task<ActionResult<DebtResponseDto>> CreateAsync(
    CreateDebtRequestDto request,
    CancellationToken cancellationToken)
{
    var result = await sender.Send(
        new CreateDebtCommand(request.ContactId, request.Amount),
        cancellationToken);

    return Ok(result);
}
```

If the handler throws a known or unexpected exception, centralized handling owns
the HTTP translation.

Use a local try/catch only when the current layer can actually recover, add
meaningful context, translate a provider-specific failure into an application
failure, or implement a bounded retry policy.

## MediatR pipeline responsibilities
Pipeline behaviors may:
- validate requests;
- add logging/context;
- manage database transactions for marked commands;
- log/rethrow exceptions.

Pipeline behaviors must not generate HTTP responses. HTTP is owned by the web
boundary.

## Transactions and errors
A transactional behavior must rollback and rethrow when the command fails.
Do not swallow an exception merely to make a transaction appear successful.

Do not keep database transactions open around slow external I/O unless a design
explicitly requires it.

## External provider failures
External APIs can fail transiently. Retry only failures that are genuinely
transient and safe to retry.

Rules:
- bound retries;
- respect `CancellationToken`;
- use exponential/backoff behavior when appropriate;
- avoid retrying validation/authentication/provider 4xx failures blindly;
- ensure retried writes are idempotent or otherwise safe;
- do not log credentials or full sensitive payloads.

Do not standardize a fixed retry count for every provider without considering the
provider contract and operation semantics.

## Background services
A background worker should survive recoverable iteration failures, but must still
honor application shutdown.

Conceptually:

```csharp
while (!stoppingToken.IsCancellationRequested)
{
    try
    {
        await RunIterationAsync(stoppingToken);
    }
    catch (OperationCanceledException) when (stoppingToken.IsCancellationRequested)
    {
        break;
    }
    catch (Exception ex)
    {
        logger.LogError(ex, "Background iteration failed");
    }

    await Task.Delay(delay, stoppingToken);
}
```

Do not catch and suppress cancellation indefinitely.

## Localization of errors
Do not require a `BaseController.T()`-style helper.

If the backend owns localized user-facing error text, resolve it through the
project's localization service at the appropriate boundary according to
`localization.md`.

Keep canonical error classification separate from translated presentation text.

## Checklist
- [ ] Application/Domain code does not know HTTP status codes or IActionResult
- [ ] Request validation runs centrally when the project uses a validation pipeline
- [ ] Domain/business rules remain in Domain/Application
- [ ] Generic errors are centralized; module-specific errors stay module-owned
- [ ] One HTTP exception handler maps errors to ProblemDetails
- [ ] 401 authentication and 403 authorization are not conflated
- [ ] Unexpected errors do not expose internal details
- [ ] Controllers do not duplicate generic try/catch logic
- [ ] Transaction failures rollback and rethrow
- [ ] Retries are bounded, safe and cancellation-aware
- [ ] Background workers honor cancellation

## Meta — Evolution
If a new error scenario needs a specific contract →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- the scenario;
- proposed canonical category/status mapping;
- whether it is an extension or correction.
