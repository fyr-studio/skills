# Naming Guidelines — Core
version: 1.0
last-updated: 2026-08
changelog:
  - 1.0: initial cross-language naming standard

## Language
New Fyr Studio code uses English for identifiers, filenames and comments.

A documented legacy project may preserve historical non-English names while migrating incrementally. Do not translate unrelated legacy code solely for cosmetics, but do not extend the legacy naming style into new concepts unless required for compatibility.

## Intent over implementation
Names should reveal domain/use-case intent rather than implementation mechanics.

Prefer:
- `remainingBalance`
- `RegisterPayment`
- `PaymentRepository`

Avoid vague names:
- `data`
- `manager`
- `helper`
- `processor`
- `doStuff`

unless the concept genuinely has that meaning.

## Follow language ecosystem conventions
Use the established casing/suffix conventions of the implementation language/framework unless a Fyr Studio layer-specific skill defines a stronger standard.

Examples:
- C#: PascalCase public types/methods, camelCase locals/parameters.
- TypeScript/JavaScript: PascalCase types/components, camelCase functions/variables.
- Constants follow the dominant ecosystem/project convention; consistency matters more than forcing one casing across languages.

## Names describe stable meaning
Do not encode temporary UI appearance, provider names or implementation details into a domain concept unless those details are actually contractual.

Prefer `paymentMethod` over `blueBankButton`.

## Comments
Comments are English and explain why, constraints or non-obvious behavior. Do not narrate obvious syntax.

## Abbreviations
Use common domain/industry abbreviations only when they improve clarity. Avoid private shorthand that makes search and onboarding harder.

## Checklist
- [ ] New identifiers/comments are English
- [ ] Names expose intent rather than generic mechanics
- [ ] Casing follows the language ecosystem
- [ ] No temporary visual/provider detail leaked into stable domain naming
- [ ] Comments explain non-obvious reasons/constraints
