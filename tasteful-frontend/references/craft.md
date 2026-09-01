# Craft floor

Load this right before writing UI code, after the direction is settled. Build to it without
announcing it. A pinned brief or the committed world overrides anything here; your own habit
does not. The floor holds the mechanics and never picks the direction.

## Tokens

- Everything flows through custom properties defined once: color roles, font roles, a type
  scale, a spacing scale on a 4-point base with semantic names, radii, easings, durations, and
  six named z-index levels (base, raised, dropdown, sticky, modal, toast/tooltip).
- No hex, OKLCH, or font-family outside the token block. If a value is needed, add the token,
  then use it.
- In Tailwind v4, tokens live in `@theme`; no arbitrary bracket values scattered across
  components. Reuse a project's existing token names rather than shadowing them.
- An existing global stylesheet is append-only: keep its framework directives where they are,
  add below, imports at the very top.

## Contrast and color

- Body and placeholder text 4.5:1; large text, icons, controls, and focus rings 3:1. Check
  computed pairs in every state and both themes.
- Any rule that sets a dark or colored background also sets its text color.
- Text on the accent uses the `accent-ink` role, never a hard-coded white.
- State is never color alone; add an icon, text, or pattern.

## Type

- Body 16 px floor, prose measure 45 to 75 ch, display capped near 6 rem, tracking floor
  -0.04 em, uppercase line-height floor 1.0.
- `text-wrap: balance` on headings, `pretty` on prose, `overflow-wrap: anywhere; min-width: 0`
  on display text so long words cannot overflow.
- Tabular numerals on data. Real punctuation. Metric-matched fallbacks; `font-display: swap`.
- Run the real copy at every breakpoint and fix what wraps badly or overflows.

## Spacing and depth

- Tight inside groups, generous between them. More space above a heading than below it.
- `gap` for sibling rhythm; margins only for optical corrections.
- Vary section padding; equal padding everywhere flattens the page.
- Shadows carry an offset and a soft blur. A zero-offset colored halo is decoration. On dark
  surfaces, elevation is lightness, about 3 percent per level.
- Depth is primarily weight, scale, and hue, not shadow. One shadow style per page.

## States

Every interactive element ships all of: default, hover, focus-visible, active, disabled,
loading, error, success, where they apply. Hover inside `@media (hover: hover)`. Disabled
carries opacity, cursor, and `aria-disabled`.

- **Focus**: `:focus-visible` with a 2 to 3 px ring, 2 px offset, 3:1 against element and page,
  instant, on everything interactive. `outline: none` without a replacement is a defect.
- **Inputs**: border width constant across all states; a reserved transparent outline slot so
  focus never shifts layout; label above, helper below with reserved height, error replaces
  helper; validate on blur then on change; `aria-invalid` and `aria-describedby`; input height
  equals button height; a reserved right-edge slot for icon or spinner.
- **Hit targets**: 44 by 44 CSS px minimum on touch, via padding or a pseudo-element overlay.
- **Empty, loading, error**: designed, not defaulted. Skeletons over spinners when the layout
  is known. Empty states name what is empty, why it matters, and the one next action.
- **Overlays**: `<dialog>` or the Popover API; `inert` behind modals; Escape closes;
  overlays escape `overflow: hidden` ancestors.
- **Undo over confirm** for reversible actions; typed confirmation for irreversible ones.

## Responsive

Verify at 360, 768, and 1280 at minimum, plus the user's own viewport when known.

- `html, body { overflow-x: clip }` (not `hidden`; `clip` keeps sticky and fixed working). No
  horizontal scroll at any width from 320 to 1920.
- Clickable text never wraps: buttons, nav links, tabs, breadcrumbs, CTAs. Shorten the label
  first, then `white-space: nowrap`, then drop or collapse items.
- Image-bearing grid tracks use `minmax(0, 1fr)`, never bare `1fr`.
- Mobile-first with `min-width` queries in rem, breakpoints where the content breaks.
  `clamp()` for continuous sizes, media queries for discrete layout changes.
- `dvh` for viewport heights. Never `100vw`. Logical properties (`margin-inline`,
  `padding-block`). Safe-area insets on fixed bars.
- Two sticky elements at `top: 0` overlap; offset the inner one by the nav height and give the
  nav the higher z-index.
- Hover is never the only path to anything. Coarse pointers get larger targets.
- On a 1280 by 800 laptop the first viewport shows the hero's essential content (headline,
  lede, primary action, the visual's focal point) without scrolling, and reads as a complete
  composition. Fix an overflowing hero by sizing display to copy length, leading at 1.0 to
  1.1, a two-line lede, and trimmed padding, never by shrinking everything.

## Images and media

- Explicit `width` and `height` or `aspect-ratio` on every image; `srcset` and `sizes`;
  `<picture>` for art direction; modern formats.
- The LCP image or video gets `fetchpriority="high"` and is never lazy-loaded. Everything below
  the fold is lazy.
- Video: `autoplay muted loop playsinline`, a poster, `preload="metadata"`.
- Decorative art is `aria-hidden`; informative art has a real accessible name.
- Verify stock or remote URLs resolve. A placeholder looks like a placeholder.

## Browser surfaces

Theme what you did not draw: `::selection`, `caret-color`, `accent-color`, scrollbars where the
world wants them (`scrollbar-color`, and keep them accessible), `text-underline-offset` and
`text-decoration-thickness`, the focus ring, `color-scheme`. These ship with defaults that
belong to no design system; theming them is the cheapest signal that the page was built.

## Accessibility and semantics

- Landmarks, a heading order with no skipped levels, labels on every control, accessible names
  on icon-only buttons, `aria-live="polite"` for async state, no `aria-live` per keystroke.
- Keyboard: everything reachable and operable, logical tab order that matches visual order,
  arrow keys where a widget expects them (tabs, menus, comboboxes).
- Reduced motion path on every animation. No flashing above 3 Hz. Respect `prefers-color-scheme`
  when the app claims dark mode. Nothing plays sound without a gesture.
- Zoom to 200 percent still works. Text can be resized without breaking layout.

## Performance

- Animate composited properties by default; bound blur, filter, canvas, and shader work to
  regions; `will-change` only during animation.
- Lazy-initialize heavy resources near the viewport; pause offscreen; target 60 fps on a
  mid-range phone.
- No dependency for what the platform expresses. Load only the font weights and axes used.
- No layout shift on load: reserved image boxes, metric-matched fallbacks, reserved helper
  heights.

## Copy and content

- The product's own language. Controls name their action. Errors name the problem and the
  recovery. No lorem ipsum, no placeholder names, no invented claims.
- Every requirement in the brief is present and findable within seconds.

## Code hygiene

- One spacing system; no specificity fights between section and element rules.
- Named z-index levels; no `9999`.
- No dead CSS, debug output, or polish-created duplication. Semantics, project conventions,
  and working behavior preserved.
