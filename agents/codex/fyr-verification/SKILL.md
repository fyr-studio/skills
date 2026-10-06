---
name: fyr-verification
description: Independent Fyr Studio acceptance-criteria verification. Use only when the user explicitly asks to independently verify completed work, audit acceptance criteria, or perform the Fyr Studio PASS/FAIL/BLOCKED verification workflow; ordinary implementation testing alone should not activate it.
---

# Fyr Verification

Adapter version: 1.0.0

This is a native Codex adapter, not a copy of the Fyr Studio verification policy.

Use this skill only for explicit independent verification. Before verifying:

1. Resolve the physical source path of this `SKILL.md`. If it was reached through a symbolic link, use the resolved path.
2. Derive the Fyr Studio repository root from that source location: the repository root is three directories above `agents/codex/fyr-verification/`.
3. If real-path resolution is unavailable, use `C:\Proyectos\skills` as the documented Windows fallback.
4. Verify that `<repo-root>/core/index.md` and `<repo-root>/core/verification.md` exist. If the canonical repository cannot be resolved, stop and report the failure; do not reproduce the policy from memory or from this adapter.
5. Read `<repo-root>/core/index.md` for prerequisite core guidance, then read `<repo-root>/core/verification.md` and follow it exactly.

Preserve the canonical PASS / FAIL / BLOCKED semantics and the distinction between automated evidence and manual verification. Do not reinterpret or duplicate the verification policy. This is the Codex equivalent of the Claude-specific wrapper at `agents/claude/fyr-verification.md`, adapted to native `SKILL.md` discovery.
