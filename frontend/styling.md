# Frontend UI & Styling Guidelines
version: 2.0
last-updated: 2026-08
changelog:
  - 2.0: replace fixed Debt Assistant palette/React Native mechanics with reusable design-system guidance
  - 1.0: initial version

## Goal
Keep UI consistent, accessible and maintainable without forcing the visual identity or styling API of one product onto another.

## Design tokens
Recurring visual decisions should come from the project's design system/tokens when scale justifies them:
- colors/semantic roles;
- typography;
- spacing;
- radius;
- elevation/shadow;
- motion where relevant.

Do not hardcode a global Fyr Studio product palette. Each product owns its visual identity; the engineering standard is to centralize repeated design decisions rather than scatter magic values.

## Semantic styling
Prefer semantic roles such as `primary`, `surface`, `error`, `success`, `textSecondary` over binding reusable components directly to arbitrary literal colors.

## Platform conventions
Use the styling/layout mechanism appropriate to the project platform (CSS/design tokens, React Native styles, Unity UI Toolkit/UGUI, native views, etc.). Do not prescribe `StyleSheet.create`, `FlatList`, CSS modules or another framework globally.

## Accessibility
Where the platform supports it:
- maintain usable contrast;
- support dynamic text/scaling when product UX permits;
- expose accessibility labels/roles for interactive controls;
- preserve focus/keyboard/controller navigation where relevant;
- do not rely on color alone to communicate critical state.

## Responsive/adaptive layout
Design for the project's supported device/window range. Avoid unexplained pixel assumptions when content can vary by locale, font scaling or viewport.

## Reuse
Create reusable components/tokens for patterns that genuinely repeat and should evolve together. Do not create a design-system abstraction for a one-off view prematurely.

## Checklist
- [ ] Repeated visual values use project-owned tokens/design system
- [ ] Product palette is not embedded in generic skills
- [ ] Styling mechanism matches the platform
- [ ] Accessibility requirements considered
- [ ] Layout tolerates supported viewports and localized string length
- [ ] Reuse follows real repetition, not speculation
