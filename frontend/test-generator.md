# Frontend Testing Guidelines
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: remove Expo/Jest/Debt Assistant-specific templates and align with risk-based core testing
  - 1.0: initial version

Follow `../core/testing.md`.

## Choose the project stack
Use the test runner and UI testing tools already established by the project/platform. Do not introduce Jest, Vitest, PlayMode tests, React Testing Library or another framework merely because a generic skill mentions it.

## Priority
High value frontend coverage usually includes:
- pure formatting/parsing/validation/state-transition logic;
- client service/adapters and response/error mapping;
- security/session-sensitive flows;
- critical reusable components with behavior;
- critical user journeys where integration risk is high;
- localization fallback/parameter behavior when custom logic exists.

Low value usually includes snapshotting trivial markup or testing visual constants solely for coverage.

## Components/views
Test observable behavior: rendered state, enabled/disabled actions, emitted events, navigation intent at the appropriate boundary and recovery from classified failures.

Avoid asserting internal hook/state implementation when user-observable behavior is enough.

## External boundaries
Mock/fake the network/storage/platform boundary rather than mocking every internal function.

Do not use production services or secrets.

## Accessibility and localization
For important components/flows, test accessibility semantics and representative localization effects when the platform/tooling supports it.

## Checklist
- [ ] Existing project test stack reused
- [ ] Highest-risk client behavior covered first
- [ ] Tests focus on observable behavior
- [ ] External boundaries isolated deterministically
- [ ] No production services/secrets used
