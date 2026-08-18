# Backend Error Handling Guidelines
version: 3.0
last-updated: 2026-08
changelog:
  - 3.0: generalize centralized error contracts beyond ASP.NET/MediatR while retaining HTTP semantics
  - 2.0: centralized ProblemDetails/validation guidance
  - 1.0: initial version

Follow `../core/error-handling.md`.

## Application/domain independence
Domain/application failures must not depend on HTTP response types or framework transport objects.

Use meaningful error types/codes/categories for validation, not-found, forbidden, conflict/business-rule and unexpected failures.

## Transport mapping
Map application failures to the transport contract at one centralized boundary where practical.

For HTTP APIs, common semantics are:
- 400 invalid request;
- 401 unauthenticated;
- 403 authenticated but forbidden;
- 404 resource absent;
- 409 state/concurrency/business conflict;
- 500 unexpected internal failure.

Use the framework's standard structured error format when appropriate (for example Problem Details) rather than ad-hoc strings.

## Validation
Syntactic/request-shape validation belongs at/near the application boundary. State-dependent business invariants remain in domain/application behavior.

## Controllers/routes
Do not duplicate generic try/catch-to-status logic in every endpoint. Local catches are for real recovery/translation/context.

## Provider/database failures
Translate provider-specific exceptions at infrastructure boundaries when they have stable application meaning. Do not expose raw provider details to clients.

## Background jobs
Recover from bounded iteration failures when appropriate but honor cancellation/shutdown and preserve diagnostic logging.

## Localization
Machine-facing API error classification remains language-neutral. See `localization.md` and `../core/localization.md`.

## Checklist
- [ ] Domain/application errors are transport-independent
- [ ] HTTP mapping is centralized/structured where practical
- [ ] 401 and 403 remain distinct
- [ ] Business invariants are not reduced to request validation
- [ ] Raw provider/internal failures are not exposed
- [ ] Cancellation is honored
