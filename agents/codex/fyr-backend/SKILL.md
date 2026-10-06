---
name: fyr-backend
description: Fyr Studio standards for backend and server work, including APIs, services, workers, persistence, databases, backend architecture, server-side errors, localization, tests, refactors, and reviews.
---

# Fyr Backend

Adapter version: 1.0.0

This is a native Codex adapter, not a copy of the Fyr Studio standards.

Before backend work:

1. Resolve the physical source path of this `SKILL.md`. If it was reached through a symbolic link, use the resolved path.
2. Derive the Fyr Studio repository root from that source location: the repository root is three directories above `agents/codex/fyr-backend/`.
3. If real-path resolution is unavailable, use `C:\Proyectos\skills` as the documented Windows fallback.
4. Verify that `<repo-root>/core/index.md` and `<repo-root>/backend/index.md` exist. If the canonical repository cannot be resolved, stop and report the failure; do not reproduce the standards from memory or from this adapter.
5. Read `<repo-root>/core/index.md` first and load every relevant core standard it selects.
6. Read `<repo-root>/backend/index.md`, then load every relevant backend standard it selects.

Do not separately invoke `fyr-engineering` merely to obtain core standards. Do not assume a framework, ORM, database, mediator, cloud, or architecture beyond the canonical standards and the current project. This adapter must remain a thin loader of the shared source of truth.
