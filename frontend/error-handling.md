# Error Handling Guidelines — Frontend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When writing try/catch blocks, handling API errors,
showing error messages to the user, or dealing with
edge cases in async operations.

## Why
Centralized error handling avoids duplicating catch logic
in every screen, ensures consistent user-facing messages,
and makes error classification predictable.

## Rules

### Always use the centralized error handler
NEVER write custom Alert messages for API errors in screens.
ALWAYS use `mostrarError` from `utils/errorHandler.ts`.

✅
```typescript
import { mostrarError } from '../../utils/errorHandler';

try {
  await saveDebt(body);
} catch (error) {
  mostrarError(error);
}
```

❌
```typescript
try {
  await saveDebt(body);
} catch (error) {
  Alert.alert('Error', 'Something went wrong. Please try again.');
}
```

### Allowed exceptions to the centralized handler

**1. Timeout / AbortError** — has its own flow with specific UI:
```typescript
} catch (error: any) {
  if (error?.name === 'AbortError') {
    Alert.alert(
      t('recording.timeoutTitle'),
      t('recording.timeoutDetail'),
      [{ text: t('recording.createManual'), onPress: handleManual }]
    );
    return;
  }
  mostrarError(error);
}
```

**2. User dismissed share sheet** — not an error, navigate normally:
```typescript
} catch (error: any) {
  if (error?.message === 'User did not share') {
    navigation.navigate('PendingDebts');
    return;
  }
  mostrarError(error);
}
```

**3. Duplicate debt (409)** — needs specific UI with actions:
```typescript
import { classifyError } from '../../utils/errorHandler';

} catch (error: any) {
  const classified = classifyError(error);
  if (classified.tipo === 'duplicado') {
    Alert.alert(t('common.warning'), t('confirmation.duplicateDetail'), [
      { text: t('confirmation.merge'), onPress: () => handleDuplicate() },
      { text: t('common.cancel'), style: 'cancel' },
    ]);
    return;
  }
  mostrarError(error);
}
```

### Always show loading state during async operations

✅
```typescript
const [isSaving, setIsSaving] = useState(false);

const handleSave = async () => {
  setIsSaving(true);
  try {
    await saveDebt(body);
    navigation.navigate('PendingDebts');
  } catch (error) {
    mostrarError(error);
  } finally {
    setIsSaving(false);
  }
};
```

❌
```typescript
const handleSave = async () => {
  await saveDebt(body); // no loading state
  navigation.navigate('PendingDebts');
};
```

### Never swallow errors silently
If you catch an error and don't show it to the user,
log it explicitly and document why it's intentional.

✅
```typescript
} catch (error) {
  // Push token registration is non-critical — silent failure is intentional
  console.log('Push token registration failed:', error);
}
```

❌
```typescript
} catch (error) {
  // empty catch — why? what happened?
}
```

## Error types reference
See `utils/errorHandler.ts` for the full list of error types:
- `network` — connection error
- `server` — 500 error
- `unauthorized` — 401 error
- `notFound` — 404 error
- `validation` — 400 error
- `duplicate` — 409 error
- `unknown` — any other error

## Checklist
- [ ] No custom Alert messages for API errors
- [ ] mostrarError used for all unhandled API errors
- [ ] AbortError handled separately with timeout UI
- [ ] 'User did not share' handled without showing error
- [ ] 409 handled with duplicate-specific UI
- [ ] Loading state shown during all async operations
- [ ] Silent catches are documented with a comment

## Meta — Evolution
If a new error case needs special handling →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The error case
- The proposed handling approach
- Whether it's an extension or correction
