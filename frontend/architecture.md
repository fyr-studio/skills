# Architecture Guidelines — Frontend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When defining folder structure, creating new modules,
deciding where to put logic, or evaluating architectural patterns.

## Why
A consistent architecture reduces cognitive load, makes the
codebase predictable and allows the AI to make correct decisions
without asking where things go.

## Rules

### Folder structure
```
project/
├── app/
│   └── screens/          ← one file per screen
├── components/           ← reusable components
├── context/              ← React context (auth, global state)
├── hooks/                ← custom hooks
├── services/             ← API calls, external services
├── utils/                ← pure utility functions
├── types/                ← TypeScript types and interfaces
├── locales/              ← i18n translation files
└── skills/               ← AI development guidelines
```

### API calls
NEVER use fetch directly in screens or components.
ALWAYS centralize in `services/api.ts`.

✅
```typescript
// services/api.ts
export const getDebtHistory = async (userId: string) => {
  const response = await fetch(`${API_URL}/api/debt/history?userId=${userId}`);
  if (!response.ok) throw new Error(`Error: ${response.status}`);
  return response.json();
};

// screens/PendingDebtsScreen.tsx
import { getDebtHistory } from '../../services/api';
const data = await getDebtHistory(userId);
```

❌
```typescript
// screens/PendingDebtsScreen.tsx — NEVER do this
const response = await fetch(`${API_URL}/api/debt/history?userId=${userId}`);
```

### State management
- Local state with `useState` for screen-level data
- `Context` only for truly global state (auth session)
- No Redux or Zustand — not needed at current scale
- `AsyncStorage` only for persistent user preferences

✅ Local state for screen data:
```typescript
const [debts, setDebts] = useState<Debt[]>([]);
const [isLoading, setIsLoading] = useState(false);
```

❌ Global state for screen data:
```typescript
// Don't put screen-specific data in Context
```

### Navigation
- All routes defined in `types/navigation.ts`
- Add new routes there before using them in App.tsx
- Never hardcode route names as strings in screens

✅
```typescript
// types/navigation.ts
export type RootStackParamList = {
  Recording: undefined;
  Confirmation: { data: ProcessedDebtResponse };
};

// screens/RecordingScreen.tsx
navigation.navigate('Confirmation', { data });
```

### Component responsibility
Each component does one thing.
If a component handles logic AND presentation, split it.

✅ Separated:
```typescript
// components/DebtCard.tsx — presentation only
// hooks/useDebtActions.ts — logic only
```

❌ Mixed:
```typescript
// components/DebtCard.tsx — handles API calls, navigation AND rendering
```

### File imports order
```typescript
// 1. React
import React, { useState, useEffect } from 'react';
// 2. React Native
import { View, Text, StyleSheet } from 'react-native';
// 3. External libraries
import { useTranslation } from 'react-i18next';
// 4. Internal — types, services, utils, components
import { Debt } from '../types';
import { getDebtHistory } from '../services/api';
```

## Checklist
- [ ] No fetch calls outside services/api.ts
- [ ] New routes added to types/navigation.ts
- [ ] Components have single responsibility
- [ ] No Redux/Zustand added without justified reason
- [ ] Imports follow the defined order

## Meta — Evolution
If a pattern emerges that doesn't fit this structure →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The pattern and why it's better
- Whether it's an extension or breaking change
