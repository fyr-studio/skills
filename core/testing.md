# Testing Principles — Core
version: 1.1
last-updated: 2026-08
changelog:
  - 1.1: add explicit deterministic retry-policy testing guidance
  - 1.0: initial cross-platform testing standard

## Goal
Tests protect meaningful behavior and architecture boundaries without becoming a second implementation that blocks change.

## Test risk, not lines
Prioritize behavior with meaningful failure cost:
- domain invariants/calculations;
- branching use cases;
- authorization/security boundaries;
- money/data integrity;
- concurrency/idempotency;
- parsing/serialization contracts;
- integrations/adapters;
- critical user flows.

Do not create tests merely to increase coverage percentage.

## Prefer the lowest valuable level
Use the smallest test boundary that proves the behavior:
- pure/unit tests for deterministic business logic;
- integration tests for database/network serialization/framework wiring that matters;
- end-to-end tests for a small set of critical flows.

Do not repeatedly test the same rule through every layer unless each layer adds contractual behavior.

## Behavior over implementation details
Tests should survive harmless refactors.

Prefer assertions about observable results, state and contracts over private calls, method order or exact internal SQL/string implementation unless that exact form is contractual.

## Deterministic retry policies
When retry timing/classification is part of system behavior, isolate the policy from provider/network execution when practical so it can be tested deterministically.

Tests should prove the externally meaningful policy: which failures retry, which are terminal, maximum attempts, and the exact/backoff delays when those delays are contractual. Control the clock rather than sleeping in tests.

Persistence/integration tests should separately verify that retry state survives process restarts when durability is required.

## Deterministic and isolated
Tests must not depend on production data, live secrets, real billing providers or uncontrolled network services.

Control time/randomness/external services when they affect reproducibility.

## Naming
Test names should communicate behavior and condition. Follow the language ecosystem while preserving that intent.

Example pattern:

```text
Operation_ExpectedBehavior_WhenCondition
```

## Fixing bugs
When practical, reproduce a bug with a failing automated test before the fix, then keep the test as regression coverage.

## Checklist
- [ ] Highest-risk behavior is covered first
- [ ] Test level is no larger than necessary
- [ ] Tests assert behavior, not incidental internals
- [ ] Retry policy is deterministic/testable when it is contractual
- [ ] No production/staging data or live secrets are used
- [ ] Time/random/network dependencies are controlled where needed
- [ ] Bug fixes add regression coverage when practical
