# Verification — Core
version: 1.0.0
last-updated: 2026-08
changelog:
  - 1.0.0: initial agent-agnostic verification standard

## Goal
Verify implemented changes independently against explicit acceptance criteria without redesigning or expanding the task.

## When to use
Use this skill when independent verification is explicitly requested or the task marks verification as required.

Verification is read-only with respect to product code and repository state unless the task explicitly authorizes a fix pass after verification.

## Operating principles
- Verify behavior and acceptance criteria; do not redesign the feature.
- Treat the current repository state as authoritative.
- Inspect the changed surface first, then expand only when required by a criterion or direct regression risk.
- Do not perform broad repository cleanup or general code review during verification.
- Do not convert unverifiable UI/editor/device/external behavior into a PASS.

## Verification sequence
Start with:
1. acceptance criteria and IDs;
2. implementation report, when available;
3. `git status --short`;
4. changed-file list and relevant diff;
5. targeted code/config/tests required to evaluate each criterion.

Run existing relevant build, test, lint or static-analysis commands when they provide useful evidence and are safe in the current environment.

Do not install/update dependencies, generate files, run destructive migrations, or rewrite repository state merely to complete verification.

## Result classification
For each acceptance criterion use exactly one result:
- `PASS` — sufficient evidence verifies the criterion;
- `FAIL` — observed implementation/behavior contradicts the criterion;
- `BLOCKED` — the environment or available evidence cannot verify it reliably.

For failures report:
- failure ID (`F-01`, `F-02`, ...);
- criterion ID;
- observed behavior;
- expected behavior;
- concise evidence;
- affected files when identifiable.

## Manual verification
List only checks that genuinely require manual execution, such as:
- Unity Editor/Inspector wiring;
- visual layout/animation judgment;
- physical-device behavior;
- external-service behavior unavailable in the environment;
- deployment/platform checks that cannot be reproduced locally.

Do not claim these items passed unless they were actually observed.

## Final report
Use a compact structure:

```text
VERIFICATION REPORT

Verdict: PASS | FAIL | BLOCKED

AC-01 PASS
AC-02 FAIL
AC-03 BLOCKED

Failures:
F-01
Criterion: AC-02
Observed: ...
Expected: ...
Evidence: ...
Affected: ...

Automated:
- <command/result summary>

Manual:
- <manual check still required>
```

Omit empty sections and avoid chronological narration.

## Checklist
- [ ] Every acceptance criterion has PASS/FAIL/BLOCKED
- [ ] PASS means actually verified
- [ ] Verification did not silently change implementation or git state
- [ ] Automated evidence is summarized accurately
- [ ] Manual checks are separated from automated verification
