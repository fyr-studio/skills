# Fyr Studio — Skills

Centralized engineering standards used by coding agents across Fyr Studio projects.

The repository defines **how Fyr Studio works**, not how one specific product happens to be implemented today.
Project repositories contain product/domain decisions, configuration, platform choices and justified exceptions.

## What is a skill?

A skill is a focused `.md` engineering standard with one responsibility. Skills should be:

- reusable across current and future Fyr Studio products;
- opinionated enough to make implementation consistent;
- independent from a specific product name;
- independent from incidental legacy code;
- explicit about trade-offs and when an exception is justified;
- versioned with semantic versioning;
- equipped with practical checks/examples when useful.

A rule belongs here when we want the same engineering principle to guide Debt Assistant, Card Slayer, JobCopilot and future products where the concern applies.

## Authority and precedence

When instructions conflict, use this order:

```text
explicit user decision for the current task
  > explicit documented project exception / ADR / product constraint
  > relevant Fyr Studio skill
  > framework or platform convention
  > incidental legacy code
```

Legacy code is not a standard. Preserve it when changing it is outside scope, but do not copy a weak legacy pattern into new code merely for local consistency.

A project may deviate from a Fyr Studio standard only when the reason is explicit and material (for example platform limitations, offline requirements, compatibility constraints or an approved legacy migration plan).

## Structure

```text
skills/
├── core/
│   ├── index.md
│   ├── engineering-principles.md
│   ├── architecture.md
│   ├── naming.md
│   ├── error-handling.md
│   ├── testing.md
│   └── localization.md
├── frontend/
│   ├── index.md
│   ├── architecture.md
│   ├── naming.md
│   ├── error-handling.md
│   ├── styling.md
│   ├── i18n.md
│   └── test-generator.md
├── backend/
│   ├── index.md
│   ├── architecture.md
│   ├── naming.md
│   ├── error-handling.md
│   ├── database.md
│   ├── localization.md
│   └── test-generator.md
├── README.md
└── CHANGELOG.md
```

`core/` owns standards that should not be redefined independently by frontend/backend skills. Frontend/backend skills adapt those standards to their layer without changing their intent.

## Versioning

Each skill has its own semantic version:

| Type | When | Version |
|---|---|---|
| Patch | wording/example only, no rule change | `1.1.0 -> 1.1.1` |
| Minor | compatible extension or clarification | `1.1 -> 1.2` |
| Major | core convention changes or prior guidance becomes invalid | `1.x -> 2.0` |

Existing historical files may use `1.0`/`2.0`; continue semantic intent even when patch precision is omitted.

## Integration in projects

### Git submodule

```bash
git submodule add https://github.com/fyr-studio/skills skills
git submodule update --remote
```

### Agent entry point

Project instructions should tell the coding agent to read the relevant orchestrator before implementation, for example:

```markdown
Read `skills/frontend/index.md` for frontend work and `skills/backend/index.md` for backend work.
```

The orchestrator then loads required core and layer-specific skills.

## Evolution

When development exposes a reusable missing or incorrect rule, report:

**[SKILL UPDATE SUGGESTED]**

A suggestion is accepted into this repository only when it generalizes beyond the current product. Product-specific knowledge belongs in that project's documentation instead.

A useful suggestion states:
- the reusable problem;
- the proposed Fyr Studio standard;
- evidence/trade-offs;
- whether it is an extension, correction or breaking change.
