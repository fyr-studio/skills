# Naming Guidelines — Frontend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When you need to name variables, functions, components,
files, types, constants or any identifier in frontend code.

## Why
Consistent naming makes code more readable, reduces friction
when searching for patterns and avoids mixing languages in
the same file. Types use English to maintain consistency
with external libraries that extend them.

> ⚠️ Legacy exception: Debt Assistant uses Spanish for variables
> and comments. All new Fyr Studio projects use English.

## Rules

### Variables and functions
camelCase in English.

✅ `const amount = 0`
✅ `const handlePayment = async () => {}`
✅ `const [isLoading, setIsLoading] = useState(false)`
❌ `const Amount = 0`
❌ `const handle_payment = async () => {}`
❌ `const [loading_state, setLoadingState] = useState(false)`

### Components
PascalCase in English.

✅ `DebtCard`
✅ `PaymentModal`
✅ `NumericKeyboard`
❌ `debtCard`
❌ `payment_modal`
❌ `numericKeyboard`

### Types and interfaces
PascalCase in English — no `I` prefix for interfaces.

✅ `Debt`
✅ `MerchantRegisteredDto`
✅ `RootStackParamList`
❌ `IDebt` ← no I prefix
❌ `DebtType` ← no Type suffix
❌ `debt` ← not PascalCase

### Constants
SCREAMING_SNAKE_CASE in English.

✅ `const MAX_RECORDING_DURATION_MS = 60000`
✅ `const MESSAGE_INTERVAL_MS = 3000`
❌ `const maxDuration = 60000`
❌ `const MAX-DURATION = 60000`

### Screen files
PascalCase + `Screen` suffix.

✅ `RecordingScreen.tsx`
✅ `PendingDebtsScreen.tsx`
✅ `SubscriptionExpiredScreen.tsx`
❌ `Recording.tsx`
❌ `recordingScreen.tsx`
❌ `Screens/Recording.tsx`

### Component files
PascalCase — no suffix.

✅ `DebtCard.tsx`
✅ `PaymentModal.tsx`
❌ `debtCard.tsx`
❌ `DebtCardComponent.tsx`

### Service and util files
camelCase.

✅ `api.ts`
✅ `paymentMessage.ts`
✅ `errorHandler.ts`
❌ `API.ts`
❌ `PaymentMessage.ts`

### Comments
Always in English. First line of each file: relative path.

✅
```typescript
// app/screens/RecordingScreen.tsx
// Main audio recording screen
```
❌
```typescript
// RecordingScreen
// pantalla principal de grabación
```

## Checklist
- [ ] Variables and functions in camelCase English
- [ ] Components in PascalCase English
- [ ] Types in PascalCase English without I prefix
- [ ] Constants in SCREAMING_SNAKE_CASE English
- [ ] Screen files with Screen suffix
- [ ] First line of file is the commented path
- [ ] No language mixing in the same identifier

## Meta — Evolution
If you find a naming case not covered →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The new case
- The suggested convention and why
- Whether it's an extension (new case) or correction (wrong rule)
