# Frontend Architecture Guidelines
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: replace React Native/Debt Assistant structure with platform-neutral client architecture guidance
  - 1.0: initial version

## Foundation
Also follow `../core/architecture.md` and `../core/engineering-principles.md`.

## Responsibilities
Keep these concerns distinct when present:
- presentation/components/views;
- screen/page/scene coordination;
- application/client state;
- domain/use-case logic shared beyond one view;
- external I/O (HTTP, storage, platform SDKs);
- navigation/routing.

Do not create layers that have no actual responsibility.

## External I/O
UI rendering code must not own raw HTTP/database/storage/provider details.

Centralize external access behind cohesive client services/adapters so authentication, serialization, retries and error classification are not duplicated across screens/components.

Do not force one giant `api.ts`; split by cohesive responsibility when growth justifies it.

## State
Use the narrowest state scope that owns the behavior:
1. local/view state;
2. feature state shared by a bounded flow;
3. application/global state only for genuinely app-wide concerns.

Do not put screen-specific state into a global store merely because one exists.

Choose the project/framework's simplest adequate state mechanism. Do not ban or mandate a library globally without evidence.

## Navigation
Routes/scenes and their parameters should be typed/validated where the platform supports it. Navigation decisions belong to flow/presentation coordination, not low-level reusable components or domain logic.

## Components
Prefer components/views with a clear responsibility and explicit inputs/outputs.

Separate presentation from orchestration when the combination creates real complexity or blocks reuse/testing; do not split every tiny component ceremonially.

## Platform boundaries
Platform-specific APIs (camera, files, notifications, billing, native plugins, Unity services, etc.) should be wrapped when doing so prevents business/feature logic from depending directly on volatile provider details.

## Persistence
Local persistence is for data/preferences that must survive process restarts. Do not use persistent storage as accidental global state or as a duplicate server source of truth.

## Checklist
- [ ] Raw external I/O is not scattered through UI code
- [ ] State uses the narrowest useful scope
- [ ] Navigation is not hidden inside low-level/domain code
- [ ] Components have coherent responsibilities
- [ ] Provider/platform details are isolated where volatility warrants it
- [ ] Persistent storage is not a competing source of truth
