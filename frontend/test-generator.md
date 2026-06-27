# Test Generator — Frontend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When generating, modifying or reviewing tests for
frontend code.

## Why
Tests catch regressions early, document expected behavior,
and give confidence when refactoring. Prioritizing utils
and services over screens keeps the test suite fast and
maintainable.

## Stack
- Jest (included with Expo)
- @testing-library/react-native
- @testing-library/jest-native

### Setup if not configured
```bash
npx expo install jest-expo @testing-library/react-native @testing-library/jest-native
```

Add to package.json:
```json
"jest": { "preset": "jest-expo" }
```

## Folder structure
```
project/
└── __tests__/
    ├── components/
    │   ├── DebtCard.test.tsx
    │   └── PaymentModal.test.tsx
    ├── screens/
    │   ├── RecordingScreen.test.tsx
    │   └── ConfirmationScreen.test.tsx
    ├── services/
    │   └── api.test.ts
    └── utils/
        ├── errorHandler.test.ts
        └── paymentMessage.test.ts
```

## Priority

**High — test these first:**
- `utils/` — pure logic, easy to test, high value
- `services/api.ts` — mock fetch and verify calls
- Critical components — DebtCard, PaymentModal

**Medium:**
- Screens — main flows only
- Error handling — errorHandler.ts

**Do not test:**
- Visual styles
- Animations
- Navigation (too coupled to the stack)

## Required mocks

Always include these mocks:

```typescript
// i18n mock — always include
jest.mock('react-i18next', () => ({
  useTranslation: () => ({
    t: (key: string) => key,
  }),
}));

// api.ts mock
jest.mock('../services/api');

// AsyncStorage mock
jest.mock('@react-native-async-storage/async-storage',
  () => require('@react-native-async-storage/async-storage/jest/async-storage-mock')
);
```

## Templates

### Utils test template
```typescript
// __tests__/utils/functionName.test.ts
import { functionToTest } from '../../utils/fileName';

describe('functionToTest', () => {
  it('handles normal case', () => {
    expect(functionToTest(input)).toBe(expected);
  });

  it('handles empty value', () => {
    expect(functionToTest('')).toBe(expected);
  });

  it('handles undefined', () => {
    expect(functionToTest(undefined)).toBe(expected);
  });

  it('handles edge case — [describe the case]', () => {
    expect(functionToTest(edgeInput)).toBe(expected);
  });
});
```

### Component test template
```typescript
// __tests__/components/ComponentName.test.tsx
import React from 'react';
import { render, fireEvent, waitFor } from '@testing-library/react-native';
import { ComponentName } from '../../components/ComponentName';

jest.mock('react-i18next', () => ({
  useTranslation: () => ({ t: (key: string) => key }),
}));

describe('ComponentName', () => {
  const defaultProps = {
    // minimum props to render
  };

  it('renders correctly', () => {
    const { getByText } = render(<ComponentName {...defaultProps} />);
    expect(getByText('expected.key')).toBeTruthy();
  });

  it('calls onAction when button is pressed', async () => {
    const mockOnAction = jest.fn();
    const { getByText } = render(
      <ComponentName {...defaultProps} onAction={mockOnAction} />
    );
    fireEvent.press(getByText('button.key'));
    await waitFor(() => expect(mockOnAction).toHaveBeenCalledTimes(1));
  });
});
```

### API service test template
```typescript
// __tests__/services/api.test.ts
import { apiFunction } from '../../services/api';

global.fetch = jest.fn();

describe('apiFunction', () => {
  beforeEach(() => {
    (fetch as jest.Mock).mockClear();
  });

  it('calls the correct endpoint', async () => {
    (fetch as jest.Mock).mockResolvedValueOnce({
      ok: true,
      json: async () => ({ success: true }),
    });

    await apiFunction('param');

    expect(fetch).toHaveBeenCalledWith(
      expect.stringContaining('/api/expected-path'),
      expect.objectContaining({ method: 'POST' })
    );
  });

  it('throws on error response', async () => {
    (fetch as jest.Mock).mockResolvedValueOnce({ ok: false, status: 500 });
    await expect(apiFunction('param')).rejects.toThrow('500');
  });
});
```

## Naming conventions

```typescript
describe('ComponentOrFunction', () => {
  it('does something in normal case', () => {});
  it('handles empty input gracefully', () => {});
  it('shows error when API fails', () => {});
  it('calls onSuccess after saving', () => {});
});
```

## Checklist
- [ ] Tests are in `__tests__/` mirroring source structure
- [ ] i18n mock included in component tests
- [ ] fetch mocked in API service tests
- [ ] Each test has a single clear assertion
- [ ] Edge cases covered (empty, undefined, error)
- [ ] No tests for styles or animations

## Meta — Evolution
If a new testing pattern is needed for a specific feature →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The feature and why existing templates don't cover it
- The proposed test pattern
- Whether it's an extension or correction
