# Styling Guidelines — Frontend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When writing styles, choosing colors, building UI components,
or making visual decisions in frontend code.

## Why
Consistent styling makes the app feel native and professional.
A shared color palette and spacing system reduces visual
inconsistency and speeds up development.

## Rules

### Always use StyleSheet.create()
NEVER use inline styles except for dynamic values.

✅
```typescript
<View style={styles.container}>
  <Text style={styles.title}>Hello</Text>
</View>

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#F5F5F5' },
  title: { fontSize: 20, fontWeight: '700', color: '#1A1A1A' },
});
```

❌
```typescript
<View style={{ flex: 1, backgroundColor: '#F5F5F5' }}>
  <Text style={{ fontSize: 20 }}>Hello</Text>
</View>
```

✅ Exception — dynamic values only:
```typescript
<View style={[styles.button, { backgroundColor: color }]}>
```

### Color palette
NEVER use hardcoded colors outside this palette.

| Token | Hex | Usage |
|---|---|---|
| Primary | `#1565C0` | Buttons, links, active states |
| Error / Action | `#D32F2F` | Errors, destructive actions, recording |
| Warning | `#F57C00` | Warnings, partial payments |
| Success | `#2E7D32` | Paid state, success messages |
| Background | `#F5F5F5` | Screen backgrounds |
| Surface | `#FFFFFF` | Cards, modals, inputs |
| Text Primary | `#1A1A1A` | Main text |
| Text Secondary | `#666666` | Secondary text, labels |
| Text Disabled | `#999999` | Placeholders, disabled states |
| Border | `#E0E0E0` | Dividers, borders |

✅ `backgroundColor: '#1565C0'`
❌ `backgroundColor: '#0000FF'` ← not in palette

### StyleSheet location
Always at the bottom of the file, after the component.

✅
```typescript
export const MyComponent = () => { ... };

const styles = StyleSheet.create({ ... });
```

❌
```typescript
const styles = StyleSheet.create({ ... });

export const MyComponent = () => { ... };
```

### Elevation and shadows
Use elevation for Android cards and modals.
Always pair with shadow props for iOS compatibility.

✅
```typescript
card: {
  elevation: 2,
  shadowColor: '#000',
  shadowOffset: { width: 0, height: 1 },
  shadowOpacity: 0.08,
  shadowRadius: 4,
},
```

### Border radius
Use consistent border radius values:
- Small elements (badges, chips): `8`
- Cards and inputs: `12`
- Buttons: `12`
- Modals (top corners only): `20`

### Typography scale
| Usage | Size | Weight |
|---|---|---|
| Screen title | 20-24 | 700 |
| Card title | 15-17 | 700 |
| Body | 14-16 | 400-500 |
| Label / Caption | 12 | 400 |
| Button | 15-17 | 600-700 |

### Spacing
Use multiples of 4 for padding and margin:
`4, 8, 12, 16, 20, 24, 32, 48`

### Performance
- Use `FlatList` for lists — NEVER `map()` inside `ScrollView`
- Set explicit dimensions on images
- Avoid anonymous functions in render for frequently updated lists

✅
```typescript
<FlatList
  data={debts}
  keyExtractor={(item) => item.id}
  renderItem={({ item }) => <DebtCard debt={item} />}
/>
```

❌
```typescript
<ScrollView>
  {debts.map((debt) => <DebtCard key={debt.id} debt={debt} />)}
</ScrollView>
```

## Checklist
- [ ] No inline styles except dynamic values
- [ ] All colors from the defined palette
- [ ] StyleSheet.create() at the bottom of the file
- [ ] Cards have elevation + shadow
- [ ] Lists use FlatList not map() in ScrollView
- [ ] Typography follows the defined scale
- [ ] Spacing uses multiples of 4

## Meta — Evolution
If a new UI pattern emerges not covered here →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The pattern
- Proposed styling approach
- Whether it's an extension or correction
