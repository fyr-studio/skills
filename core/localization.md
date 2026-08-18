# Localization & Internationalization — Core
version: 1.0
last-updated: 2026-08
changelog:
  - 1.0: establish Fyr Studio localization standard with English canonical fallback

## Goal
All Fyr Studio products should handle languages and locale-sensitive data predictably without rebuilding localization architecture for each project.

## English is canonical and mandatory fallback
English (`en`) is the canonical source language and fallback for Fyr Studio products.

The English catalog/contract must be complete for all user-facing strings owned by the application.

A missing/unsupported requested locale resolves toward English rather than exposing raw keys or broken UI.

## No hardcoded user-facing UI text
User-facing application text must be resolved through the project's localization abstraction.

The skill defines the behavior, not a library: React/i18next, Unity String Tables, native platform catalogs or another suitable implementation may satisfy the same contract.

## Semantic keys
Use stable semantic keys describing meaning/context, not the English sentence itself and not transient visual appearance.

Preferred shape:

```text
common.actions.cancel
common.errors.network
debt.payment.remainingBalance
settings.language.title
```

Namespaces may use as many levels as clarity requires; do not force an arbitrary depth.

Before adding a key, search for an existing semantically equivalent key.

## English defines the key contract
English is the authoritative key set.

Every locale marked as production-supported must satisfy that contract before release. Runtime fallback protects users; it is not a substitute for release-time completeness validation.

Translation placeholders/parameters for the same key must remain compatible across locales.

## Client applications remain usable offline
Client applications must ship with enough bundled localization to boot and provide their core UI without network access.

Remote localization may be used as a validated/cacheable override or content-update mechanism, but it must not be required for basic application usability.

Do not create a state where the application needs a network translation pack merely to explain that the translation download failed.

## Interpolation, pluralization and grammar
Do not concatenate localized sentence fragments or implement English-specific plural logic in application code.

Use named parameters plus the platform/localization system's plural/select/message-format capabilities.

Parameter names are stable and not translated.

## Locale-aware formatting
Numbers, money, dates, times and units use locale-aware platform facilities rather than manual separators/string concatenation.

Currency is business data; formatting is locale-sensitive presentation.

## Separate concepts
Do not infer these concepts from each other unless an explicit product rule does so:
- UI language;
- locale;
- country/region;
- currency;
- timezone;
- phone/numbering region.

For example a user may use English UI while operating a Vietnamese business with VND and Asia/Ho_Chi_Minh timezone.

## Language resolution
Default client resolution order:
1. explicit persisted user choice;
2. best supported device locale/language (exact locale, then language when appropriate);
3. English.

Changing country/currency must not silently change the user's explicit UI language.

## API and backend responsibility
Machine-facing APIs should return stable machine-readable error/status codes and structured parameters, not localized presentation strings intended for the client UI.

The client maps those codes to its own localization keys.

The backend localizes content only when the backend itself delivers the final human-facing message, for example email, SMS, push-notification body or generated document without a client presentation layer.

Those server-rendered messages use the recipient's explicit/persisted locale with English fallback.

## Validation/tooling
Where practical, automated checks should verify:
- English key completeness;
- production locale completeness;
- compatible placeholder sets;
- referenced keys exist;
- hardcoded user-facing strings are prevented/detected;
- fallback behavior works;
- representative plurals/long strings/locale formats render correctly.

## Checklist
- [ ] English source/fallback is complete
- [ ] No hardcoded user-facing application strings
- [ ] Semantic existing keys are reused where possible
- [ ] Production locales satisfy the English key contract
- [ ] Client core UI works without network localization
- [ ] Dynamic grammar uses localization parameters/plural rules
- [ ] Money/date/number/unit formatting is locale-aware
- [ ] Language, locale, country, currency and timezone remain distinct
- [ ] APIs expose machine-readable codes instead of client UI language
