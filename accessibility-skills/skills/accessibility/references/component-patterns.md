# Component Accessibility Patterns

Detailed ARIA patterns and keyboard interactions for common interactive components. Use these as reference when building custom widgets.

## Dialog (Modal)

```html
<div role="dialog" aria-modal="true" aria-labelledby="title" aria-describedby="desc">
  <h2 id="title">Confirm</h2>
  <p id="desc">Are you sure?</p>
  <button>Cancel</button>
  <button>Confirm</button>
</div>
```

**Keyboard:** Tab within dialog, Escape to close, Enter to activate focused button.

**Focus:** First focusable on open → trap within → return to trigger on close.

---

## Tabs

```html
<div role="tablist" aria-label="Settings">
  <button role="tab" aria-selected="true" aria-controls="panel-1">General</button>
  <button role="tab" aria-selected="false" aria-controls="panel-2" tabindex="-1">Security</button>
</div>
<div role="tabpanel" id="panel-1" aria-labelledby="tab-1">...</div>
<div role="tabpanel" id="panel-2" aria-labelledby="tab-2" hidden>...</div>
```

**Keyboard:** Arrow Left/Right to navigate tabs, Home/End for first/last, Tab into panel content.

**Focus:** Roving tabindex — only active tab has `tabindex="0"`.

---

## Menu

```html
<button aria-haspopup="true" aria-expanded="false" aria-controls="menu-1">Options</button>
<ul role="menu" id="menu-1">
  <li role="menuitem" tabindex="-1">Edit</li>
  <li role="separator"></li>
  <li role="menuitem" tabindex="-1">Delete</li>
</ul>
```

**Keyboard:** Enter/Space to open and activate, Arrow Up/Down to navigate, Escape to close, letter key to jump.

**Focus:** First item on open, roving tabindex within.

---

## Combobox (Autocomplete)

```html
<label for="city">City</label>
<input id="city" role="combobox" aria-autocomplete="list" aria-expanded="false"
       aria-controls="city-list" aria-activedescendant="">
<ul role="listbox" id="city-list" hidden>
  <li role="option" id="opt-1">New York</li>
  <li role="option" id="opt-2">Los Angeles</li>
</ul>
```

**Keyboard:** Arrow Down to open/navigate, Arrow Up for previous, Enter to select, Escape to close, typing filters.

**Focus:** Input retains focus, `aria-activedescendant` tracks visual focus in listbox.

---

## Accordion

```html
<h3>
  <button aria-expanded="true" aria-controls="sect-1">Section 1</button>
</h3>
<div id="sect-1" role="region" aria-labelledby="btn-1">Content</div>

<h3>
  <button aria-expanded="false" aria-controls="sect-2">Section 2</button>
</h3>
<div id="sect-2" role="region" aria-labelledby="btn-2" hidden>Content</div>
```

**Keyboard:** Enter/Space to toggle, Arrow Up/Down between headers, Home/End for first/last.

---

## Listbox

```html
<label id="label">Choose color</label>
<ul role="listbox" aria-labelledby="label" tabindex="0" aria-activedescendant="opt-blue">
  <li role="option" id="opt-red">Red</li>
  <li role="option" id="opt-blue" aria-selected="true">Blue</li>
</ul>
```

**Keyboard:** Arrow Up/Down to navigate, Home/End, letter key to jump, Space to select.

---

## Tooltip

```html
<button aria-describedby="tip-1">Settings</button>
<div role="tooltip" id="tip-1" hidden>Configure settings</div>
```

**Behavior:** Show on hover and focus, hide on Escape/blur/mouse leave. No interactive content inside.

---

## Slider

```html
<label for="vol">Volume</label>
<input type="range" id="vol" min="0" max="100" value="50"
       aria-valuemin="0" aria-valuemax="100" aria-valuenow="50" aria-valuetext="50%">
```

**Keyboard:** Arrow Right/Up to increase, Arrow Left/Down to decrease, Home/End for min/max, Page Up/Down for large steps.

---

## Focus Management Techniques

### Roving Tabindex

For composite widgets (tabs, menu, listbox): one item has `tabindex="0"`, all others `tabindex="-1"`. Arrow keys move the `0` between items.

### aria-activedescendant

Alternative to roving tabindex: container keeps focus, `aria-activedescendant` points to the visually focused child by ID. Preferred for combobox and some listbox implementations.

### Focus Trap

For dialogs and modals: intercept Tab at last focusable → wrap to first, and Shift+Tab at first → wrap to last.

---

## Common ARIA States

| Attribute | Values | Used For |
|-----------|--------|----------|
| `aria-expanded` | true/false | Expandable widgets (accordion, menu trigger) |
| `aria-selected` | true/false | Tabs, listbox options |
| `aria-checked` | true/false/mixed | Checkboxes, switches |
| `aria-pressed` | true/false | Toggle buttons |
| `aria-hidden` | true/false | Hide from assistive technology |
| `aria-disabled` | true/false | Non-interactive disabled state |
| `aria-current` | page/step/true | Current item in navigation |
| `aria-invalid` | true/false | Form validation state |
| `aria-errormessage` | ID ref | Points to error message element |
