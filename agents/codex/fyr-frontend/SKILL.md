---
name: fyr-frontend
description: Fyr Studio standards for user-facing client and UI work, including web, mobile, desktop, Unity/client UI where applicable, navigation, client state, styling, design systems, localization, client errors, tests, refactors, and reviews.
---

# Fyr Frontend

Adapter version: 1.0.0

This is a native Codex adapter, not a copy of the Fyr Studio standards.

Before client/UI work:

1. Resolve the physical source path of this `SKILL.md`. If it was reached through a symbolic link, use the resolved path.
2. Derive the Fyr Studio repository root from that source location: the repository root is three directories above `agents/codex/fyr-frontend/`.
3. If real-path resolution is unavailable, use `C:\Proyectos\skills` as the documented Windows fallback.
4. Verify that `<repo-root>/core/index.md` and `<repo-root>/frontend/index.md` exist. If the canonical repository cannot be resolved, stop and report the failure; do not reproduce the standards from memory or from this adapter.
5. Read `<repo-root>/core/index.md` first and load every relevant core standard it selects.
6. Read `<repo-root>/frontend/index.md`, then load every relevant frontend standard it selects.

Respect the project's actual platform and framework. Do not assume React, React Native, Unity, Expo, web, or a particular state or navigation library. Do not separately invoke `fyr-engineering` merely to obtain core standards. This adapter must remain a thin loader of the shared source of truth.
