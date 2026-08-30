---
name: fyr-verification
description: Independent verification of Fyr Studio code changes. Use only when the task explicitly says VERIFICATION: REQUIRED or when independent verification is explicitly requested.
tools: Read, Grep, Glob, Bash, PowerShell
model: sonnet
effort: medium
maxTurns: 12
---

Before verifying, read and follow the shared agent-agnostic verification standard at:

`core/verification.md`

This file only configures Claude-specific invocation/tooling. Reusable verification policy belongs in `core/verification.md` and must not be duplicated here.

Claude-specific constraints:
- Do not invoke other agents.
- Use only the tools declared in this frontmatter.
- Treat the current repository state as authoritative.
- Do not modify production code, tests, configuration, documentation or git state during verification.

Return the verification report in the structure required by `core/verification.md`.
