# Server-Side Localization Guidelines — Backend
version: 1.0
last-updated: 2026-08
changelog:
  - 1.0: initial version

## When to use this skill
Use this skill when backend code owns or serves localized content, validates
supported locales, renders localized notifications/messages, or exposes locale
packs/manifests to clients.

## Why
Localization becomes a backend concern when server-generated content must match
the user's language or when clients consume backend-owned translation packs.
Without one consistent pattern, localized strings get duplicated in services,
supported locales drift between endpoints, and fallback behavior becomes
unpredictable.

## Core principles

### One runtime source of truth
If the backend owns runtime localization, localized user-facing text must come
from the backend locale packs rather than being duplicated as hardcoded strings
inside controllers, background services, or notification services.

Keep locale packs versioned with the application and load them into a read-only
service at startup when practical. Do not query a database or external
translation API on every request unless the product explicitly requires dynamic
content management.

### Supported locales come from configuration
Do not maintain a second hardcoded list of supported locales inside controllers.
The localization service/manifest is authoritative.

When persisting a locale preference:
1. trim input;
2. normalize to the project's canonical locale form;
3. validate it against the loaded/supported locale set;
4. persist the canonical locale.

Unknown locales must not be silently persisted.

### English fallback
English is the default Fyr Studio fallback locale unless a project explicitly
defines another fallback.

Fallback must be deterministic:
1. requested locale + requested key;
2. English + requested key;
3. controlled failure/logging when the key is missing from the fallback pack.

Do not silently return an unrelated translation key or raw internal identifier.

## Translation lookup
Expose one small reusable lookup contract rather than parsing JSON separately in
each caller.

Recommended conceptual API:

```csharp
string? GetTranslation(string locale, string key);
```

Dotted keys are preferred for nested JSON packs:

```text
notification.debtReminderTitle
payment.accountNumber
errors.validation
```

The localization service may flatten nested JSON into a lookup dictionary at
startup if that keeps runtime lookup simple and allocation-free.

Do not make controllers/background services understand the storage structure of
the locale files.

## Template substitution
Localized templates may contain named placeholders:

```text
{{name}}
{{amount}}
{{currency}}
```

Rules:
- use named placeholders, never positional placeholders;
- keep placeholder names identical across all locale packs;
- substitute only known placeholders supplied by the caller;
- do not translate placeholder names;
- do not build full localized sentences through string concatenation;
- format domain values (money, dates, names) before inserting them into the
  localized template when the formatting belongs to that domain.

Example conceptual flow:

```text
locale pack template
        ↓
resolve requested locale with English fallback
        ↓
format domain values
        ↓
replace {{namedPlaceholders}}
        ↓
user-facing message
```

If a required placeholder is missing, prefer a controlled error/log over
sending a broken template to the user.

## Locale pack validation
When locale packs are application configuration, validate them at startup when
possible.

For required/contractual keys verify:
- every supported locale contains the key;
- values are non-null and non-whitespace;
- structured dictionaries that must stay aligned have identical key sets;
- placeholder sets match across translations for the same template.

Fail fast for configuration drift that would make the application serve broken
localized content.

Do not hide configuration incompleteness behind runtime English fallback.
Runtime fallback and configuration completeness are separate concerns.

## Manifest, versioning and client delivery
When clients download backend-owned locale packs:
- expose the supported locale list from the backend;
- provide a stable version/hash per locale;
- use ETag / `If-None-Match` when supported;
- return `304 Not Modified` for unchanged packs;
- keep English available as the fallback pack;
- do not create a second endpoint for individual translated fields when the
  locale pack already owns them.

The client may cache locale packs, but the backend repository remains the source
of truth for their contents.

## Background services and notifications
Background jobs do not get an HTTP `Accept-Language` context automatically.
They must load the persisted user/merchant locale with the data needed for the
job, resolve it through the localization service, and apply the same fallback
rules as request-driven code.

Never assume the deployment region, business country, currency, or phone number
implies UI language.

## Keep localization separate from domain canonicalization
Localization validates and resolves locales and translated text.

Country codes, currency codes, timezones, payment providers, and other domain
identifiers must be canonicalized by their own configuration/domain services.
Do not move those responsibilities into the localization service merely because
they are used near localized UI.

## Error handling
Localized user-facing errors should follow the project's error-handling contract.
Do not expose raw internal exceptions or configuration details as translated UI
messages.

For missing localization configuration:
- log locale + key;
- avoid logging sensitive user values;
- use deterministic fallback where allowed;
- fail startup when the missing key violates a validated configuration contract.

## Checklist
Before delivering backend localization work verify:
- [ ] Locale packs are the single backend source of localized server content
- [ ] Supported locales come from the localization service/manifest
- [ ] Persisted locale values are normalized and validated
- [ ] English fallback is deterministic
- [ ] Callers use a reusable lookup API rather than parsing locale JSON directly
- [ ] Template placeholders are named and consistent across locales
- [ ] Domain values are formatted by the appropriate domain logic before interpolation
- [ ] Required localization keys are validated for completeness
- [ ] Background services use persisted locale, not deployment/business assumptions
- [ ] Client-delivered packs use version/hash/ETag semantics when applicable
- [ ] Domain canonicalization remains outside localization

## Meta — Evolution
If localization requirements change materially — for example plural rules,
gender-aware grammar, ICU MessageFormat, runtime translation management, or a
CMS-backed translation pipeline — report with **[SKILL UPDATE SUGGESTED]** and
state whether the change is an extension or breaking change.
