# Engineering Principles — Core
version: 1.1.0
last-updated: 2026-08
changelog:
  - 1.1.0: expand implementation delivery report and blocked-validation requirements
  - 1.0.0: initial standard

## Why
Fyr Studio is developed by a solo developer using coding agents. The workflow must reduce rework, prevent agents from making hidden product decisions and keep changes reviewable.

## Design before code
When a task contains a real product/domain/architecture ambiguity, do not choose silently and start coding.

Before implementation:
1. identify the decision;
2. compare materially different valid options and trade-offs;
3. resolve the decision explicitly;
4. then implement the chosen design.

Cosmetic or locally obvious implementation details do not require unnecessary questions.

## Investigation before assumption
When the repository state is unclear, inspect the relevant code/config/contracts first.

Examples:
- whether a capability already exists;
- which module owns a concept;
- what API/schema contract is authoritative;
- how the current project performs deployment, persistence or localization.

Report what was found before making a design decision that depends on that evidence.

## Scope discipline
Make the smallest coherent change that satisfies the approved design.

Do not:
- refactor unrelated code opportunistically;
- upgrade packages without need;
- redesign adjacent modules because they could be cleaner;
- mix cleanup with a behavioral change unless required for correctness.

If unrelated problems are discovered, report them separately.

Explicit no-touch boundaries in the task are requirements, not suggestions. Preserve unrelated user work and repository state.

## Simplicity with an extraction path
Prefer architecture that is clean enough to evolve without paying complexity costs before evidence exists.

Default posture:
- clear module/responsibility boundaries now;
- simple deploy/runtime topology now;
- extraction/distribution later when concrete pressure justifies it.

Do not introduce microservices, message buses, generic abstractions, plugin systems or other infrastructure only because a future product might need them.

## Explicit trade-offs
When several valid approaches exist, state what each optimizes and what it gives up. Do not present a preference as objectively superior when the choice depends on constraints.

## Source of truth
Every important concept should have one authoritative representation: schema, contract, config, design token, translation key set, etc.

Do not create parallel sources merely to make a local implementation easier.

## Automation agents are implementers, not product owners
Coding agents may resolve routine implementation details, but they must not invent product/domain decisions that materially affect behavior, data ownership, pricing, permissions, user-visible semantics or architectural direction.

If such a decision is missing, stop that subtask and report the ambiguity.

## Delivery report
For non-trivial implementation tasks, provide one final report containing the whole handoff rather than fragmented per-file updates.

Include:
- investigation findings and relevant existing architecture discovered;
- decisions made, including how routine ambiguities were resolved;
- unresolved material ambiguities, if any;
- files changed and the behavior implemented;
- verification actually performed, including exact build/test/lint/static-check commands when useful and their results;
- validation that was attempted but blocked by the environment, stated explicitly as blocked rather than implied as passing;
- manual wiring or UI/editor/deployment steps still required, or `None`;
- unrelated issues noticed but intentionally not changed;
- explicit confirmation of important no-touch boundaries when the task called them out;
- git metadata required by `git-workflow.md` when git operations are part of the task.

Do not claim tests, builds, UI behavior, Editor wiring or deployment checks passed unless they were actually executed/observed.

## Checklist
- [ ] Real ambiguity resolved before implementation
- [ ] Relevant repository state investigated instead of assumed
- [ ] Change stayed inside scope
- [ ] No speculative complexity added
- [ ] One source of truth preserved
- [ ] Verification and blocked validation are reported accurately
- [ ] Manual follow-up is explicit
- [ ] Git handoff metadata is included when applicable
