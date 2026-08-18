# Engineering Principles — Core
version: 1.0
last-updated: 2026-08
changelog:
  - 1.0: initial standard

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
For non-trivial implementation tasks, report together:
- investigation findings;
- decisions made and unresolved ambiguities;
- files changed;
- verification performed/results;
- manual wiring or UI/editor/deployment steps still required;
- unrelated issues noticed but intentionally not changed.

## Checklist
- [ ] Real ambiguity resolved before implementation
- [ ] Relevant repository state investigated instead of assumed
- [ ] Change stayed inside scope
- [ ] No speculative complexity added
- [ ] One source of truth preserved
- [ ] Verification and manual follow-up reported
