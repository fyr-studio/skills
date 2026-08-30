# Git Workflow — Core
version: 1.0.0
last-updated: 2026-08
changelog:
  - 1.0.0: initial cross-project branch, commit and PR workflow

## Goal
Keep agent changes isolated, reviewable and easy to integrate across parallel work.

## Default workflow
For non-trivial repository changes:
1. fetch the latest remote state;
2. start a dedicated task branch from the latest `origin/main` unless the task explicitly requires another base;
3. keep one coherent task per branch;
4. implement and validate the scoped change;
5. commit the complete coherent change together;
6. push the task branch;
7. open a pull request against `main` when PR-based integration is available.

Do not work directly on `main` unless the current task explicitly authorizes it.

## Branch naming
Use a short intent-revealing branch name such as:
- `feature/<name>`
- `fix/<name>`
- `refactor/<name>`
- `docs/<name>`

If the execution environment enforces a prefix or naming scheme (for example `codex/...`), use the enforced form rather than renaming solely for convention. Report the actual branch name.

## Parallel work and stale bases
When multiple task branches were created from an older `main`, synchronize the branch with current `main` before integration and resolve conflicts intentionally.

Prefer merging current `main` into the task branch for active parallel agent work when preserving branch history and avoiding rewritten remote history is more important than linear history.

Do not rebase a shared/pushed task branch unless the task explicitly calls for it and the consequences are understood.

Conflict resolution must preserve the intended behavior from both already-integrated work and the current task. Do not resolve conflicts by blindly choosing one side.

## Scope and git safety
Do not:
- mix unrelated fixes into the task branch;
- overwrite or discard uncommitted user changes;
- force-push unless explicitly authorized;
- reset, clean, stash, checkout or otherwise alter unrelated user work without clear need and approval;
- merge or close a PR unless the task explicitly asks for integration.

## Commit discipline
Commit messages should describe the behavior or purpose of the coherent change. Avoid one commit per file unless the task genuinely consists of independently reviewable changes.

Before reporting completion, inspect the final working-tree state.

## Delivery metadata
When git is available, the final implementation report must include:
- actual branch name;
- commit SHA for the implementation, when committed;
- whether the branch was pushed to origin;
- PR status/link when one was created;
- final `git status` summary;
- any uncommitted files and why they remain.

If the environment cannot perform one of these operations, report it as blocked rather than implying it happened.

## Checklist
- [ ] Task started from the intended current base
- [ ] One coherent task is isolated on the branch
- [ ] Existing user work was not discarded
- [ ] Parallel-main changes were integrated safely when needed
- [ ] Validation happened before handoff
- [ ] Branch/commit/push/final-status metadata is reported
