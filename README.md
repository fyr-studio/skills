# Fyr Studio — Skills

Centralized repository of skills for Fyr Studio projects.
Each skill defines a single responsibility and is used by AI
as a development guide.

## What is a skill?

A skill is a `.md` file that tells the AI exactly how to work
in a specific area. Each skill:

- Has a single responsibility (Single Responsibility Principle)
- Is open for extension, closed for frequent modification
- Has its own semantic versioning
- Includes concrete examples of correct vs incorrect
- Includes a validation checklist

## Structure

```
skills/
├── frontend/
│   ├── index.md           ← frontend orchestrator
│   ├── naming.md          ← naming conventions
│   ├── architecture.md    ← architecture and patterns
│   ├── error-handling.md  ← error handling
│   ├── styling.md         ← styles and UI
│   ├── i18n.md            ← internationalization
│   └── test-generator.md  ← test generation
├── backend/
│   ├── index.md           ← backend orchestrator
│   ├── naming.md          ← naming conventions
│   ├── architecture.md    ← architecture and patterns
│   ├── error-handling.md  ← error handling
│   ├── database.md        ← database patterns
│   └── test-generator.md  ← test generation
├── README.md              ← this file
└── CHANGELOG.md           ← global change history
```

## Versioning

Each skill has its own semantic versioning:

| Type | When | Version |
|---|---|---|
| **Extension** | New examples or clarifications | Minor (1.0 → 1.1) |
| **Correction** | Incorrectly defined rule | Minor (1.1 → 1.2) |
| **Breaking** | Core convention change | Major (1.x → 2.0) |

⚠️ A Breaking change may affect existing code — review
compatibility before applying.

## Integration in projects

### Via Git Submodule
```bash
git submodule add https://github.com/fyr-studio/skills skills
```

### Update skills in a project
```bash
git submodule update --remote
```

### Reference in CLAUDE.md
```markdown
For any coding task, read `skills/frontend/index.md`
```

## Contributing

If you find a better pattern or a missing rule during
development, report it with the prefix:

**[SKILL UPDATE SUGGESTED]**

And describe what would change, why, and whether it's
an extension, correction, or breaking change.
