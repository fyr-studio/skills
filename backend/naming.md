# Backend Naming Guidelines
version: 3.0
last-updated: 2026-08
changelog:
  - 3.0: generalize naming across backend languages while preserving intent-based conventions
  - 2.0: .NET modular/CQRS naming
  - 1.0: initial version

Follow `../core/naming.md`.

## General
Use English-first names and the implementation language's ecosystem casing conventions.

Name application operations by intent:
- `CreateDebt`
- `RegisterPayment`
- `GetOrderById`
- `ListInvoices`

When the project uses command/query/handler naming, keep the operation name stable across those roles.

## Contracts
Name persistence/external ports after the domain capability they provide. Avoid generic base repositories/services unless a real abstraction has been designed.

## Transport types
Request/response/DTO naming should make transport purpose clear when useful, but do not suffix every data object mechanically.

## Files/modules
Primary public types/modules should be easy to locate from their names. Follow project/language filename conventions rather than imposing C# or TypeScript casing globally.

## Provider leakage
Do not put a provider name into a domain/application concept unless that provider-specific behavior is the actual abstraction.

## Checklist
- [ ] English-first names
- [ ] Ecosystem casing respected
- [ ] Use-case names communicate intent
- [ ] Generic Manager/Helper/Base abstractions avoided
- [ ] Provider/transport details do not leak into domain names unnecessarily
