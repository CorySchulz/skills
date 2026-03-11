---
name: web-components
description: >
  Conventions and patterns for building Magic Spells web components. Applies when creating,
  modifying, or reviewing any custom element in the @magic-spells ecosystem. Covers class
  structure, naming, CSS, events, project layout, and communication patterns.
---

# Magic Spells Web Component Conventions

These are the standards for all `@magic-spells` web components. Follow them exactly.

---

## Terminology

- Always **"show/hide"**, never "open/close"
- Always **"panel"** or **"dialog"**, never "drawer"
- Semantic prefixes: **`previous`**, **`current`**, **`next`** — never `old`, `new`

---

## Class Structure

### Method Organization

- **`queryDOM()`** — all `querySelector` / `querySelectorAll` calls go here
- **`attachListeners()`** — all event binding goes here
- **`this.handlers = {}`** — object for storing bound handler references (for cleanup)

### Naming & Style

- Private fields with `#` prefix
- Full variable names (`viewportHeight` not `vh`, `selectedOption` not `selOpt`)
- Object parameters for methods with multiple args: `({ currentIndex, previousIndex })`
- `const _ = this` shorthand **only** when 4+ `this` usages in a single method
- camelCase for methods and properties
- kebab-case for component tag names and file names

### Documentation

- JSDoc comments on classes and public methods
- No inline comments unless logic is non-obvious

---

## Project Structure

```
component-name/
  src/
    components/        # Web component classes (.js)
    styles/            # Plain CSS files (.css)
    index.js           # Entry point with named exports
  dist/                # Built output
  demo/
    index.html         # Demo page
    component-name.js  # Demo-specific JS
    component-name.css # Demo-specific CSS
  package.json
  rollup.config.mjs
  CLAUDE.md
```

- **`.css` files only** — no SCSS, no preprocessors
- **ES2024** standard
- **Named exports only** — no default exports

---

## CSS Patterns

- **Custom properties** for runtime theming (e.g. `--select-border-color`)
- **Tag names as selectors** — use the custom element tag directly (`select-dropdown { }`)
- **Attribute selectors for state** — `select-dropdown[visible]`, not class toggles
- **`rem` units** for all sizing
- No utility classes, no BEM — keep it semantic to the component

---

## Events

- **Custom events namespaced with component name**: `select-dropdown:change`, `select-dropdown:show`
- **`CustomEvent`** with `bubbles: true`
- Data attributes for behavior:
  - `data-action-*` for interactive triggers (e.g. `data-action-toggle`)
  - `data-content-*` for content identifiers

---

## Component Communication

- **Light DOM** — no Shadow DOM
- **Child-to-parent** via `this.closest('parent-component')`
- **Parent-to-child** via `querySelector` within the parent's subtree
- **Cross-component** via `CustomEvent` bubbling

---

## Elements Pattern

Each component family follows this element hierarchy:

| Role | Naming Pattern | Example |
|---|---|---|
| Root container | `<component-name>` | `<select-dropdown>` |
| Interactive trigger | `<component-trigger>` | `<select-trigger>` |
| Content panel | `<component-panel>` | `<select-panel>` |
| Selectable item | `<component-option>` | `<select-option>` |
| Visual separator | `<component-divider>` | `<select-divider>` |
| Group heading | `<component-label>` | `<select-label>` |

---

## Accessibility

- WAI-ARIA attributes kept in sync with component state
- `role` attributes on all custom elements (`listbox`, `option`, `separator`, `presentation`)
- Keyboard navigation support (arrow keys, Enter, Escape, Home, End)
- `aria-expanded`, `aria-selected`, `aria-activedescendant` updated in real time

---

## Form Integration

- Hidden `<input>` element inside the root component for native form submissions
- `value` attribute on option elements (not `data-value`)
- `selected` attribute for preselected options
