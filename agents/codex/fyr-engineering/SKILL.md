---
name: fyr-engineering
description: General Fyr Studio engineering standards for non-trivial cross-cutting, architecture, repository, refactoring, testing, error-handling, naming, localization, review, or multi-layer work. Prefer fyr-backend or fyr-frontend when the task is clearly limited to one layer.
---

# Fyr Engineering

Adapter version: 1.0.0

This is a native Codex adapter, not a copy of the Fyr Studio standards.

Before non-trivial engineering work:

1. Resolve the physical source path of this `SKILL.md`. If it was reached through a symbolic link, use the resolved path.
2. Derive the Fyr Studio repository root from that source location: the repository root is three directories above `agents/codex/fyr-engineering/`.
3. If real-path resolution is unavailable, use `C:\Proyectos\skills` as the documented Windows fallback.
4. Verify that `<repo-root>/core/index.md` exists. If the canonical repository cannot be resolved, stop and report the failure; do not reproduce the standards from memory or from this adapter.
5. Read `<repo-root>/core/index.md` and follow its "Load by concern" rules. Always load `engineering-principles.md` for non-trivial implementation, then load every other relevant core standard.

Preserve the precedence model defined by the canonical repository. When the task is clearly backend-only or frontend/client-only, prefer the corresponding layer adapter instead of activating this general adapter unnecessarily.
