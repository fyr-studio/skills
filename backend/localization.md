# Server-Side Localization Guidelines — Backend
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: align backend localization with English canonical fallback and client-bundled localization standard
  - 1.0: initial server-side localization guidance

`../core/localization.md` is authoritative.

## When backend owns localized text
The backend should localize only when it delivers the final human-facing content itself, such as:
- email;
- SMS;
- push notification title/body;
- generated document/report;
- other server-rendered message with no client presentation layer.

Machine-facing API responses intended for a client UI should expose stable language-neutral codes/structured parameters instead of localized presentation text.

## English fallback
English is the mandatory Fyr Studio fallback for server-rendered localized content.

Resolution:
1. persisted/explicit recipient locale;
2. best supported locale/language match;
3. English.

Do not infer language from deployment region, country, currency, phone prefix or business location.

## Source of truth
Server-owned message catalogs/templates have one authoritative source. Do not duplicate the same server message across controllers/workers/providers.

The storage mechanism is project-specific: bundled resources, files, compiled catalogs, a validated content system, etc.

## Templates
Use semantic keys and named parameters. Keep parameter contracts compatible across production locales. Use proper locale/plural/message-format capabilities where grammar requires them.

## Client locale delivery
A backend may expose optional localization metadata/remote overrides when the product needs dynamic copy updates, but the client core UI must not depend on downloading those packs to boot. English/basic supported localization remains bundled client-side per the core standard.

## Validation
Validate supported server message catalogs for English completeness, production locale completeness and parameter compatibility before deployment where practical.

## Checklist
- [ ] Backend localizes only final server-delivered human content
- [ ] APIs use language-neutral machine-readable codes for client UI errors/statuses
- [ ] English fallback is deterministic
- [ ] Locale is explicit/persisted, not inferred from country/currency
- [ ] Server templates use one source of truth and named parameters
- [ ] Remote client localization is optional, not a boot dependency
