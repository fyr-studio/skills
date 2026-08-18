# Frontend Error Handling Guidelines
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: replace Debt Assistant-specific handlers/cases with reusable client error boundaries
  - 1.0: initial version

Follow `../core/error-handling.md` and `i18n.md`.

## Central classification
Frontend integrations should translate transport/provider failures into a stable client error model once, close to the integration boundary.

UI code should not parse arbitrary backend message strings to decide behavior.

## Presentation
Map classified errors to localized user-facing messages/actions at the presentation layer.

Use feature-specific UI when the user can meaningfully recover (retry, re-authenticate, edit invalid input, resolve conflict, continue offline, etc.). Otherwise use the project's standard generic error presentation.

Do not require one global alert/toast component for every platform; require consistent classification and presentation semantics.

## Async state
Any user-triggered async operation must provide sufficient feedback to prevent duplicate/confusing actions when latency matters. Loading indicators, disabled actions, optimistic updates or background progress are valid depending on the UX.

Always restore transient state on success, failure and cancellation.

## Non-critical failures
A failure may be intentionally non-blocking (analytics, best-effort cache, optional token registration). It still needs an intentional policy: log/telemetry where appropriate and no empty catch.

## Connectivity/offline
When the product supports offline/degraded behavior, network failure should be distinguishable from server/business failure so the UI can offer the correct recovery path.

## Checklist
- [ ] Transport/provider failures become stable client classifications
- [ ] UI does not branch on arbitrary backend message text
- [ ] User messages/actions are localized and appropriate to recovery
- [ ] Async operations expose adequate progress/disabled/optimistic state
- [ ] Non-critical failures are intentional, not silently swallowed
