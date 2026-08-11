# Architecture Guidelines — Backend
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: replace legacy Shared/BaseController architecture with modular monolith + Clean Architecture, CQRS boundaries, module-owned DI and explicit cross-module facades
  - 1.0: initial version

## When to use this skill
When creating or refactoring backend modules, endpoints, use cases, services,
folder structure, dependency injection, module boundaries, or cross-module
communication.

## Why
Fyr Studio backends should stay simple enough for one developer to maintain while
keeping business boundaries clean enough to scale without a structural rewrite.
The default is a modular monolith: one deployable, explicit modules, clean
dependency direction, and no premature distributed-system complexity.

Project-specific architecture documented in the repository may refine these
rules. Do not silently override an explicit project decision with a generic skill.

## Core rules

### Modular monolith by default
Do not introduce microservices because a product may scale someday.

Keep one deployable backend until there is concrete evidence that a module needs
independent deployment, ownership, scaling, isolation, or technology choices.

A future extraction path is a benefit of good module boundaries, not a reason to
create distributed infrastructure now.

### Clean Architecture inside each business module
The standard module shape is:

```text
Modules/
  <Module>/
    Domain/
    Application/
    Infrastructure/
    Presentation/
    DependencyInjection.cs
    IPublic<Module>.cs        # optional — only when another module consumes it
```

Do not create empty folders merely to satisfy the diagram. Add a layer when the
module actually has code owned by that layer.

Responsibilities:

- `Domain` — business entities, value objects, invariants, domain-specific errors.
- `Application` — use cases, commands/queries, handlers, validators, repository
  ports and application contracts.
- `Infrastructure` — Dapper/Npgsql repositories, external providers, file/network
  integrations and implementations of Application ports.
- `Presentation` — ASP.NET controllers and HTTP transport concerns.
- `DependencyInjection.cs` — registrations owned by that module.

Dependency direction:

```text
Presentation -> Application -> Domain
Infrastructure -> Application / Domain
```

Forbidden:

```text
Domain -> Infrastructure
Domain -> Presentation
Application -> Presentation
Application -> ASP.NET HTTP types
```

Domain code must not depend on Dapper, Npgsql, HTTP, configuration providers,
external SDKs, or mediator libraries.

### CQRS without Vertical Slice ceremony
For projects using CQRS, commands and queries live inside the module's
`Application` layer and handlers own individual use cases.

Prefer explicit command/query handlers over large application `Service` classes
that accumulate unrelated use cases.

Do not interpret CQRS as a requirement to create a deep folder tree per feature.
Use the simplest organization that keeps commands, queries, validators and
handlers discoverable.

Mediator libraries are implementation details. Respect the package/version pinned
by the project and do not perform major upgrades without an explicit design
review.

### Thin controllers
Controllers are HTTP adapters.

They should typically:
1. read route/body/query transport input;
2. construct a command/query;
3. send it through the application mediator;
4. return the successful result.

Controllers must not:
- contain business rules;
- access repositories directly;
- open database transactions;
- call AI/external providers directly;
- repeat tenant/business authorization logic;
- contain repetitive try/catch blocks;
- coordinate multi-module writes themselves.

Do not introduce a shared `BaseController` for domain validation, tenant lookup,
localization, or authentication. Put those concerns in the correct middleware,
pipeline behavior, module, or service.

### Common is infrastructure, not shared business code
When a project uses a common cross-cutting area, use narrowly owned categories
such as:

```text
Common/
  Authentication/
  Cqrs/
    Behaviors/
  Database/
  Errors/
  Web/
```

`Common` means **no business module owns this concern**. It does not mean "used by
multiple modules".

Do not recreate dumping grounds such as:

```text
Shared/Models
Shared/DTOs
Shared/Contracts
Common/Models
Common/DTOs
Common/Contracts
Helpers
Utils
```

If a type has business meaning, put it in the module that owns that meaning.

### Cross-module communication uses explicit public facades
A module must not consume another module's internal Domain, Application,
Infrastructure, repositories, or internal services.

When module A genuinely needs module B, B may expose one minimal root-level
facade:

```text
Modules/Contacts/IPublicContact.cs
Modules/Billing/IPublicBilling.cs
```

Convention:
- singular name: `IPublicContact`, not `IPublicContacts`;
- create it only after a real cross-module dependency exists;
- expose the smallest stable capability the consumer needs;
- return small public contract records, not Domain entities;
- do not simply expose internal repositories/services through properties;
- small public records may live in the same file until growth justifies a folder.

Another module should depend only on that explicit public contract.

Do not create `IPublicX` speculatively for every module.

### Business/tenant scope is explicit
If a product has tenant/business-scoped data, the tenant/business identifier
should be explicit in the use case/API rather than inferred from "the only one"
when the domain may support multiple later.

Authentication establishes identity. The owning business/tenant module establishes
access to a scoped resource. Do not combine authentication identity, tenant,
billing and business profile into one god-object.

Repeated scope authorization belongs in middleware/pipeline behavior owned by the
relevant module, not duplicated in each handler.

### Module-owned dependency injection
Keep `Program.cs` compositional and small.

Each module registers its own internals in its `DependencyInjection.cs`.
A root composition extension may call module registrations, but it should not
know every repository implementation inside every module.

Conceptually:

```csharp
builder.Services
    .AddApi(builder.Configuration)
    .AddAuthentication(builder.Configuration)
    .AddDatabase(builder.Configuration)
    .AddApplication()
    .AddModules(builder.Configuration);
```

Do not put dozens of `AddScoped` registrations directly in `Program.cs`.

### Background services belong to a module
Use `BackgroundService` for recurring in-process work when that is sufficient.
The worker and its registration belong to the module that owns the behavior.

Do not keep a worker alive by swallowing cancellation. Handle operational
failures, log them, and allow requested shutdown to propagate.

### Avoid speculative patterns
Do not add aggregates, factories, domain events, message buses, generic
repositories, generic UnitOfWork abstractions, or extra assemblies just because
they are common architecture vocabulary.

Introduce a pattern when it solves a real rule or coupling problem.

## Extraction rule
Consider extracting a module from the monolith only when evidence justifies the
operational cost, for example:
- materially different scaling requirements;
- independent deployment is repeatedly needed;
- stronger fault/security isolation is required;
- a separate team or ownership boundary exists;
- a technology constraint cannot reasonably live in the monolith.

One signal alone is not automatically sufficient. Compare the operational cost
before extracting.

## Checklist
- [ ] Modular monolith remains the default unless extraction is justified
- [ ] Each module follows clean dependency direction
- [ ] Controllers are thin HTTP adapters
- [ ] Application use cases do not depend on HTTP
- [ ] Business types are owned by modules, not Common/Shared dumping grounds
- [ ] Cross-module dependencies use a minimal `IPublic<Module>` facade when needed
- [ ] No speculative `IPublicX` files were created
- [ ] Program.cs is compositional; module DI stays module-owned
- [ ] Tenant/business access is not inferred from client-supplied identity
- [ ] No architecture pattern was added without a concrete problem

## Meta — Evolution
If a new architectural pattern is needed →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- the pattern and the problem it solves;
- why the existing architecture does not cover it;
- whether it is an extension, correction or breaking change.
