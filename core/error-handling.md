# Error Handling Principles — Core
version: 1.0
last-updated: 2026-08
changelog:
  - 1.0: initial standard

## Goal
Failures should be classified once, preserve diagnostic context, avoid leaking internals and produce predictable user behavior.

## Separate error domains
Distinguish at least:
- invalid input/validation;
- authentication/authorization;
- resource absence;
- state/conflict/business-rule failure;
- transient external/network failure;
- cancellation/timeouts;
- unexpected internal failure.

Do not collapse all failures into a generic string too early.

## Handle at the boundary that can act
Catch an error only when the current layer can recover, translate it to a more meaningful contract, add useful context, perform a bounded retry or intentionally convert it to a user-visible state.

Otherwise allow it to propagate to the centralized boundary for that layer.

Never leave empty catches.

## Preserve machine-readable classification
APIs and internal boundaries should prefer stable codes/types/structured fields over parsing human-readable messages.

User-facing localization happens at the presentation boundary that owns the message.

## Safe user messages
Unexpected failures expose a generic user-safe message while logs/telemetry retain enough context to diagnose the issue.

Never expose secrets, tokens, SQL, stack traces, internal file paths or raw provider responses containing sensitive data.

## Retries
Retry only failures that are plausibly transient and operations that are safe/idempotent to retry.

Retries must be bounded, cancellation-aware and use an appropriate backoff strategy.

## Cancellation is not a normal error
Respect cancellation/shutdown signals and do not swallow them inside generic retry/recovery loops.

## Checklist
- [ ] Error classification remains structured
- [ ] Errors are caught only where recovery/translation is possible
- [ ] Unexpected user messages are safe
- [ ] Sensitive internals are not exposed
- [ ] Retries are bounded and safe
- [ ] Cancellation propagates correctly
