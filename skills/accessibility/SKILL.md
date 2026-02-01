---
name: accessibility
description: >
  This skill should be used when the user asks to "make this accessible",
  "add ARIA", "keyboard navigation", "screen reader support", "focus management",
  "accessible component", "WCAG compliance", "a11y", or when building interactive
  UI components that require accessibility considerations.
---

# Web Accessibility

Conventions and enforcement rules for building accessible web interfaces. Prioritize semantic HTML, apply ARIA only when native elements are insufficient, and verify every interactive component meets keyboard and screen reader requirements.

## Rule 1: Semantic HTML First

Prefer native HTML elements over ARIA roles. Native elements provide built-in keyboard behavior, screen reader semantics, and form participation for free.

| Need | Use | Not |
|------|-----|-----|
| Button | `<button>` | `<div role="button" tabindex="0">` |
| Link | `<a href>` | `<span role="link" tabindex="0">` |
| Checkbox | `<input type="checkbox">` | `<div role="checkbox">` |
| Text input | `<input type="text">` | `<div contenteditable>` |
| Heading | `<h1>`–`<h6>` | `<div role="heading">` |
| List | `<ul>/<ol>` | `<div role="list">` |
| Nav | `<nav>` | `<div role="navigation">` |

ARIA is appropriate only for patterns that have no native equivalent: tabs, combobox, dialog, tree, menu, accordion.

## Rule 2: Every Interactive Element Must Be Keyboard Accessible

Required keyboard patterns per component:

| Component | Enter/Space | Arrow Keys | Escape | Tab |
|-----------|-------------|------------|--------|-----|
| Button | Activate | — | — | Focus |
| Dialog | — | — | Close | Trap focus |
| Tabs | Activate tab | Navigate tabs | — | Into panel |
| Menu | Activate item | Navigate items | Close | — |
| Combobox | Select option | Navigate options | Close list | — |
| Accordion | Toggle section | Navigate headers | — | Focus |
| Listbox | Select | Navigate options | — | Focus |

## Rule 3: Labels and Descriptions

Every form control and interactive widget must have an accessible name.

```html
<!-- Visible label (preferred) -->
<label for="email">Email</label>
<input id="email" type="email">

<!-- aria-label (when visual label absent) -->
<button aria-label="Close dialog">×</button>

<!-- aria-labelledby (reference existing text) -->
<h2 id="section-title">Settings</h2>
<div role="dialog" aria-labelledby="section-title">

<!-- aria-describedby (supplementary description) -->
<input aria-describedby="hint">
<p id="hint">Must be at least 8 characters</p>
```

## Rule 4: Focus Management

- **Dialog open**: Move focus to first focusable element inside. Trap focus within dialog. Return focus to trigger on close.
- **Dynamic content**: When content is added/removed, manage focus so users aren't stranded.
- **Roving tabindex**: For composite widgets (tabs, menu, listbox), one item in tab order at a time; arrow keys move between items.
- **Never use positive tabindex**: Only `tabindex="0"` (in tab order) or `tabindex="-1"` (focusable by script only).

## Rule 5: Live Regions for Dynamic Updates

```html
<!-- Status updates (non-urgent) -->
<div aria-live="polite" aria-atomic="true">3 items in cart</div>

<!-- Errors (urgent) -->
<div role="alert">Connection lost</div>
```

Use `aria-live="polite"` for most updates. Reserve `aria-live="assertive"` or `role="alert"` for errors and time-critical messages only.

## Rule 6: Color and Visual

- Text contrast: 4.5:1 minimum (WCAG AA), 3:1 for large text (18pt+)
- UI component contrast: 3:1 minimum
- Never rely on color alone — always pair with icon, text, or pattern
- Respect `prefers-reduced-motion` for animations

## Rule 7: Testing Requirements

Every accessible component must pass:

1. **Keyboard-only**: Tab through all interactive elements, activate with Enter/Space, navigate composites with arrows, close with Escape
2. **Screen reader**: Page title announced, headings navigable, form labels announced, dynamic updates announced
3. **Automated**: Run axe-core or similar — zero violations
4. **Zoom**: Content usable at 200% zoom, no horizontal scroll at 400%

## Implementation: React Aria Components

For custom interactive widgets in React, use [React Aria Components](https://react-spectrum.adobe.com/react-aria/) as the default implementation library. It provides built-in ARIA, keyboard handling, focus management, and i18n.

```tsx
// Preferred: React Aria Components (high-level)
import { Button, Dialog, Tabs, ComboBox } from 'react-aria-components'

// Alternative: React Aria hooks (when full control needed)
import { useButton, useDialog } from 'react-aria'
```

Choose RAC components when building custom UI. Only drop to hooks when component-level API doesn't provide enough control over rendering.

---

## Additional Resources

### Reference Files

- **`references/component-patterns.md`** — ARIA patterns, keyboard interactions, and implementation examples for dialog, tabs, menu, combobox, accordion, listbox, and other composite widgets
