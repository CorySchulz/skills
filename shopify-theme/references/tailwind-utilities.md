# Tailwind Utilities Reference

Complete reference for all custom Tailwind v4 utilities in the Luna theme.

---

## Typography

### Dynamic Font Size — `fs-*`
Converts a pixel value to rem: `fs-18` → `font-size: calc(1rem / 16 * 18)` = `1.125rem`

Common values: `fs-10`, `fs-12`, `fs-14`, `fs-16`, `fs-18`, `fs-20`, `fs-24`, `fs-28`, `fs-32`, `fs-36`, `fs-48`, `fs-64`, `fs-72`, `fs-96`, `fs-144`

### Dynamic Font Weight — `fw-*`
`fw-400` → `font-weight: 400`

Common values: `fw-300`, `fw-400`, `fw-500`, `fw-600`, `fw-700`, `fw-800`, `fw-900`

### Font Family Utilities

| Utility | Maps to |
|---|---|
| `font-body` | `--font-body` + `--font-body-weight` |
| `font-heading` | `--font-heading` + `--font-heading-weight` |
| `font-subheading` | `--font-subheading` + `--font-subheading-weight` |
| `font-accent` | `--font-accent` + `--font-accent-weight` |
| `font-helvetica` | Helvetica Neue, Helvetica, Arial, sans-serif |
| `font-georgia` | Georgia, Times New Roman, Times, serif |

### Heading Classes — `.h1` through `.h6`
Typography presets using heading font variables (`--h1-font-size`, `--h1-line-height`, `--h1-letter-spacing`, etc.). Defined in `styles/base/_typography.css`.

### Special Typography

| Class | Purpose |
|---|---|
| `.overline-heading` | Uses `--overline-*` variables (font, size, weight, spacing, transform, color) |
| `.subheading` | Uses `--subheading-*` variables |
| `.product-card-title` | Card-specific font preset |
| `.collection-card-title` | Card-specific font preset |
| `.article-card-title` | Card-specific font preset |
| `.blog-card-title` | Card-specific font preset |

### Line Height

| Token | Value |
|---|---|
| `leading-0` | 0 |
| `leading-tightest` | 1 |
| `leading-tighter` | 1.25 |
| `leading-tight` | 1.375 |
| `leading-normal` | 1.5 |
| `leading-1` | 1.5 |
| `leading-2` | 1.625 |
| `leading-3` | 1.77 |
| `leading-4` | 2 |
| `leading-5` | 2.25 |
| `leading-6` | 2.5 |

### Letter Spacing

| Token | Value |
|---|---|
| `tracking-0` | 0 |
| `tracking-1` | 0.025em |
| `tracking-2` | 0.05em |
| `tracking-3` | 0.1em |
| `tracking-4` | 0.15em |
| `tracking-5` | 0.2em |

---

## Color Scheme

These utilities work within a `color-{scheme}` context. All use oklch color space.

### Background Colors

| Utility | Purpose |
|---|---|
| `bg-scheme` | Background with gradient support (`--color-background-gradient`) |
| `bg-scheme-fg` | Foreground color as background |
| `bg-scheme-primary` | Primary button background color |
| `bg-scheme-secondary` | Secondary button background color |
| `bg-scheme-input-bg` | Input background color |

### Text Colors

| Utility | Purpose |
|---|---|
| `text-scheme` | Foreground text color |
| `text-scheme-bg` | Background color as text |
| `text-scheme-primary` | Primary button foreground color |
| `text-scheme-secondary` | Secondary button foreground color |
| `text-scheme-input-fg` | Input foreground color |
| `text-scheme-icons` | Icon color |
| `text-scheme-links` | Link color |

### Border Colors

| Utility | Purpose |
|---|---|
| `border-fg` | Foreground border |
| `border-bg` | Background border |
| `border-scheme-input-border` | Input border |

### Static Colors

| Token | Value |
|---|---|
| `color-error` | `#b80000` |
| `color-error-bg` | `#ffdfe2` |
| `color-link` | `var(--color-link)` |

---

## Layout

| Utility | Properties |
|---|---|
| `container` | `max-width: var(--container-max-width); margin-inline: auto; padding-left/right: var(--spacing-gutter)` |
| `flex-center` | `display: flex; align-items: center; justify-content: center` |
| `absolute-fill` | `position: absolute; inset: 0; width: 100%; height: 100%; top: 0; left: 0` |
| `center-x` | `position: absolute; left: 50%; transform: translateX(-50%)` |
| `center-y` | `position: absolute; top: 50%; transform: translateY(-50%)` |
| `center-xy` | `position: absolute; left: 50%; top: 50%; transform: translate(-50%, -50%)` |
| `hide-scrollbar` | Hides scrollbar cross-browser (webkit + firefox + IE) |

---

## Spacing

### Section Padding

| Utility | Properties |
|---|---|
| `py-section` | `padding-top: calc(var(--section-padding) * 0.85); padding-bottom: var(--section-padding)` |
| `pt-section` | `padding-top: calc(var(--section-padding) * 0.85)` |
| `pb-section` | `padding-bottom: var(--section-padding)` |

Note: Top padding is 85% of bottom padding for optical balance.

### Spacing Scale (Responsive)

Values change at breakpoints. Base → lg (1024px):

| Token | Base (mobile) | lg (1024px+) |
|---|---|---|
| `spacing-xxs` | 8px | 10px |
| `spacing-xs` | 10px | 12px |
| `spacing-sm` | 12px | 16px |
| `spacing-md` | 16px | 24px |
| `spacing-lg` | 24px | 32px |
| `spacing-xl` | 32px | 48px |
| `spacing-2xl` | 48px | 64px |
| `spacing-3xl` | 64px | 96px |

Additional responsive tokens: `spacing-gap`, `spacing-gutter`, `spacing-spacer` (section spacer), `spacing-header`, `spacing-cart-gutter`, `spacing-product-form-gap`

### Section Padding by Breakpoint

| Breakpoint | `--section-padding` | `--section-spacer` |
|---|---|---|
| Base | 40px | 20px |
| sm (576px) | 45px | 20px |
| md (768px) | 50px | 30px |
| lg (1024px) | 60px | 40px |
| xl (1280px) | 80px | 50px |

---

## Sizing

| Utility | Purpose |
|---|---|
| `w-slide` | `width: var(--tarot-slide-width)` — Tarot carousel slide width |
| `aspect-product-card` | `aspect-ratio: var(--product-thumbnail-img-ratio)` |

### Height Utilities

| Token | Value |
|---|---|
| `h-auto` | auto |
| `h-100` | 100% |
| `h-100vh` | 100vh |
| `h-100svh` | 100svh |
| `h-100lvh` | 100lvh |
| `h-100dvh` | 100dvh |
| `h-header` | `var(--header-height)` |
| `h-hero` | `calc(100svh - var(--announcement-bar-height) - var(--header-height))` |

---

## Transitions

| Utility | Duration |
|---|---|
| `transition-fast` | `all 200ms ease-in-out` |
| `transition-medium` | `all 400ms ease-in-out` |
| `transition-slow` | `all 700ms ease-in-out` |

---

## Z-Index Layers

| Utility | Value |
|---|---|
| `z-header` | 100 |
| `z-cart` | 200 |
| `z-modal` | 300 |

---

## Breakpoints

| Token | Min-width |
|---|---|
| `sm` | 576px |
| `md` | 768px |
| `mde` | 896px |
| `lg` | 1024px |
| `xl` | 1280px |
| `2xl` | 1536px |
| `3xl` | 1920px |

Usage: `md:flex-row`, `lg:gap-8`, `mde:grid-cols-3`

---

## Border Radius

| Utility | Maps to |
|---|---|
| `rounded-inputs` | `var(--content-corner-radius)` |
| `rounded-buttons` | `var(--button-radius)` |
| `rounded-theme` | `var(--content-corner-radius)` |
| `rounded-theme-sm` | `var(--content-corner-radius-sm)` |

---

## Border Widths

Available via Tailwind `border-{n}`:

| Token | Value |
|---|---|
| `border-0` | 0px |
| `border-1` | 1px |
| `border-2` | 2px |
| `border-3` | 3px |
| `border-4` | 4px |
| `border-5` | 5px |

---

## Buttons

### Base — `.button`
Core button with padding, font, min-width, border, radius, and transition from CSS variables.

### Style Modifiers

| Class | Effect |
|---|---|
| `.button-solid` | (default) Filled background |
| `.button-outline` | Transparent bg, colored border + text; inverts on hover |
| `.button-tonal` | Lighter tinted background with contrast text |
| `.button-text` | No background/border, text only; subtle bg on hover |
| `.button-transparent` | 15% opacity bg with 8px backdrop blur |

### Color Modifiers

| Class | Colors |
|---|---|
| `.button-primary` | Primary button background/foreground variables |
| `.button-secondary` | Secondary button background/foreground variables |
| `.button-white` | White background, dark foreground |
| `.button-black` | Black background, white foreground |

### Size Modifier

| Class | Effect |
|---|---|
| `.button-small` | Reduced padding (`0.5rem`) and font size (`0.75rem`) |

### Usage Pattern
```html
<a class="button button-primary button-outline" href="{{ s.link }}">
  {{ s.button_text }}
</a>
```

---

## Cart Panel Variants

Custom Tailwind variants for cart state:

```css
@custom-variant cart-empty (cart-panel[state="empty"] &);
@custom-variant cart-has-items (cart-panel[state="has-items"] &);
```

Usage: `cart-empty:hidden`, `cart-has-items:block`

---

## Source Files

- `styles/tailwind/tailwind.css` — Main config (breakpoints, spacing, colors, radius)
- `styles/tailwind/typography.css` — fs-*, fw-*, font families
- `styles/tailwind/layout.css` — container, flex-center, absolute-fill, centering
- `styles/tailwind/spacing.css` — py-section, pt-section, pb-section
- `styles/tailwind/colors.css` — Color scheme utilities
- `styles/tailwind/animations.css` — Transition utilities
- `styles/tailwind/sizes.css` — w-slide, aspect-product-card
- `styles/tailwind/z-index.css` — Z-index layers
- `styles/tailwind/cart-panel.css` — Cart state variants
- `styles/base/_layout.css` — Responsive spacing values
- `styles/base/_typography.css` — Heading classes, overline, font scale
- `styles/elements/buttons.css` — Button component system
