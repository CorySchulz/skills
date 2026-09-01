# Motion

Motion is where a modern surface most clearly feels made rather than generated, and also where
generated pages most reliably give themselves away. The difference is not amount. It is
authorship: one moment the page has earned, plus small confirmations that make the interface
feel alive under the hand, and nothing that makes anyone wait.

## Principles

- **Every animation has a job**: acknowledge an action, make a state change or spatial
  relationship legible, preserve continuity through navigation, direct attention at a moment
  that matters, or embody the world's material. If you cannot name the job, cut it.
- **One authored moment, not scattered effects.** A page gets one orchestrated entrance or one
  signature interaction. Not the same fade-up on every section. Not a lift on every card.
- **Fast.** UI motion stays under 300 ms. Long feedback reads as latency. Raycast has almost no
  animation and feels right; a 500 ms entrance on a menu never will.
- **Natural.** Things decelerate into place and accelerate away. Larger elements move slower
  than smaller ones. Duration scales with distance. Nothing appears from nowhere: entrances
  start at scale 0.95 or so, never 0. A deflated balloon still has a shape.
- **Interruptible.** State can change mid-animation without a jump. Prefer CSS transitions and
  the Web Animations API over keyframes for stateful UI; keyframes can't be reversed halfway.
- **Never animate a keyboard-initiated action.** Keyboard users act faster than choreography.
- **Never animate a focus ring in.** It appears instantly, always.
- **Composited when possible.** Transform and opacity are the reliable floor. Reach past them
  on purpose, in bounded regions: blur and backdrop-filter, clip-path and mask, shadow, color,
  variable-font axes, gradient position via `@property`. Never animate layout properties
  (width, height, top, left, margin, padding); use transforms, FLIP, or `grid-template-rows`.
- **Content is visible by default.** A page whose script fails must still show everything.

## Budget by mode

- **Persuade / Experience**: one choreographed entrance (capped around 500 to 800 ms total),
  one signature interaction that belongs to this subject, scroll-driven motion when the scroll
  relationship itself means something (a diagram assembling, a product revealing, a value
  counting), hover and press feedback on controls. Ambient motion (a shader, a marquee, a
  breathing texture) only when it is the world's material, bounded, paused offscreen.
- **Operate / Read**: feedback and continuity only. 120 to 250 ms on nearly everything. No
  load choreography; a tool loads into a task. Skeletons over spinners. View transitions
  between states where continuity helps orientation.

## Tokens

```css
:root {
  --ease-out:    cubic-bezier(0.16, 1, 0.3, 1);   /* entering, hover, most UI */
  --ease-in:     cubic-bezier(0.7, 0, 0.84, 0);   /* leaving */
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);  /* moving between two on-screen states */
  --ease-soft:   cubic-bezier(0.25, 0.1, 0.25, 1); /* gentle, for toasts and long fades */

  --dur-instant: 100ms;  /* press, toggle tick, color shift */
  --dur-fast:    180ms;  /* hover, tooltip, small reveal */
  --dur-base:    260ms;  /* menu, popover, tab crossfade */
  --dur-slow:    400ms;  /* modal, drawer, accordion, section reveal */
  --dur-hero:    700ms;  /* the one authored entrance */
}
```

Easing decision: entering or exiting → `--ease-out` / `--ease-in`; moving or morphing while
visible → `--ease-in-out`; hover state → `--ease-out`; constant motion (marquee, progress) →
`linear`. Springs replace easings only for physical interactions: drag release, swipe to
dismiss, a picker snapping, a toggle handle. Stiffness around 180 and damping around 22 gives
a snappy spring with a whisper of overshoot; 280/26 is stiff with almost none. Overshoot on
buttons, modals, and tooltips is a 2018 tell.

Exits run about 20 percent faster than entrances. `transition: all` is banned; name the
properties.

## The native toolkit (zero bytes)

Prefer these to a motion library. Add a dependency only for what the platform cannot express.

- **View Transitions**: same-document for state changes and SPA routes, cross-document for
  MPA navigation. A list item expanding into its detail page, a button morphing into a dialog,
  a card resolving into a header. Name elements with `view-transition-name`; keep the
  transition under 300 ms; give the old and new states matching aspect where possible.
- **`@starting-style`**: animate from `display: none` to visible with CSS only. The correct way
  to enter popovers, dialogs, and details.
- **Scroll-driven animations**: `animation-timeline: scroll()` and `view()` run on the
  compositor with no listeners. Use for progress indicators, a reveal tied to how far an
  element has entered, a diagram that assembles as you scroll. Wrap in `@supports` and give a
  static fallback that still looks designed. Disable under 40 rem viewports unless the effect
  is the point.
- **`@property`**: register custom properties with a type so gradients, hue, and numeric values
  interpolate. The way to animate a gradient position or a counter without JS.
- **Web Animations API**: for sequencing, interruption, and dynamic values. Composable,
  cancellable, reversible.
- **Popover API and `<dialog>`**: light-dismiss, stacking, focus trap, and `::backdrop` for
  free, with `@starting-style` for the entrance.
- **`text-wrap`, `field-sizing`, `interpolate-size`, anchor positioning**: small platform
  details that remove whole categories of JS.
- **Canvas, WebGL, shaders**: when the world names the technique. Lazy-initialize near the
  viewport, pause offscreen, target 60 fps on a mid-range phone, and keep the CSS fallback
  good.

## Recipes

**Button press.** `transform: scale(0.97)` or `translateY(1px)` on `:active`, 100 ms in, 150
ms out, ease-out. Hover: one signal (background, or a 1 px lift, or an underline change),
never four at once. Focus ring instant. Animate a child, not the parent, if hover flickers.

**Input focus.** Border width constant across every state; the focus signal is an outline or
box-shadow that was already reserved at 2 px transparent, so nothing shifts. Background tint on
hover, 180 ms. Label float, if used, 200 ms ease-out and removed under reduced motion.

**Tooltip.** Hover delay 800 to 1000 ms; focus delay 0 ms. Enter 150 ms opacity plus a 4 px
travel from the trigger; `transform-origin` at the trigger. Skip the delay and the animation
for the second tooltip in a row. Hoverable, persistent, dismissible with Escape.

**Popover, menu, dropdown.** Open 180 ms ease-out from scale 0.96 and opacity 0, origin at the
trigger, optional 20 to 30 ms item stagger when there are eight or fewer items. Close 140 ms
ease-in. Flip near the edge. Light-dismiss.

**Modal, drawer.** Backdrop fades 300 ms. Content 260 to 300 ms ease-out from scale 0.96 (modal)
or from its edge (drawer). Close 220 ms ease-in. `<dialog>` with `inert` behind it, first focus
on the first interactive element. Reduced motion: opacity crossfade at 150 ms.

**Tab change.** The indicator slides (`transform`, 250 ms ease-out); the content crossfades
(100 ms out, 150 ms in, 50 ms offset). Never slide content sideways; never animate the panel's
height directly.

**Accordion.** `grid-template-rows: 0fr` to `1fr`, 300 ms ease-in-out. Or `interpolate-size:
allow-keywords` where supported.

**Toast.** Slides in 400 ms ease-out at a fixed viewport corner, dwells 4 to 6 s, exits 300 ms
ease-in. Existing toasts never reposition when a new one arrives. Pause on hover and focus.
Toasts are for failures and invisible effects; a visible success is silent.

**Copy to clipboard.** The label swaps to "Copied" with a check for 2.5 s. No toast.

**Optimistic update.** The UI changes immediately. Success does nothing. Failure animates the
rollback (200 ms) and shows a toast with one Undo. Reversible destructive actions use
optimistic delete plus Undo instead of a confirm dialog.

**Command palette.** Opens instantly, no animation. The selection highlight moves (120 ms
ease-out); the rows do not. Items stagger in on first open only, never on filter.

**Number reveal.** Counts from a nearby value to the target over 400 to 1200 ms, easing applied
to the value, `Intl.NumberFormat` for separators, final value announced once with `aria-live`.
Reduced motion renders the final value. Only for a real number the page is arguing with.

**Spinner and skeleton.** Skeleton when the layout is known. If a spinner, delay showing it
150 ms and keep it visible at least 300 ms once shown so it never flashes.

**Page entrance (Persuade, once).** Stagger by DOM index through a custom property, 40 to 80 ms
apart, total under about 600 ms, from opacity 0 and 8 to 16 px of travel, ease-out. Then the
page is simply there. Under reduced motion, a 150 ms opacity fade and no travel.

**Scroll reveal (when earned).** One-shot with IntersectionObserver or `view()` timeline, never
re-firing, never on body text, never on every section. Reserve for the moment a mechanism is
shown: a diagram assembling, a comparison resolving, a figure arriving.

**Marquee.** `translateX` over 40 to 60 s, linear, infinite, duplicated content for the seam,
paused on hover and focus, stops under reduced motion and shows the first items. Once per
page at most.

**Hover on images and cards.** One property. A crop shift (`scale(1.03)` on the image inside a
clipped frame) or a color shift or an underline; not lift plus scale plus shadow plus tint.

**Signature interaction.** Derived from the subject's mechanism: a dial that turns, a map that
routes, a fabric that responds to the cursor, a waveform that plays, a type specimen whose axes
you drag. Built with the technique the world names, with a fallback that still stands.

## Reduced motion

Every animation has a `prefers-reduced-motion: reduce` path with an intentional alternative,
not a blanket kill. Spatial motion collapses to opacity crossfades at 150 ms or less; feedback
that confirms an action stays legible; loaders and progress still run. Nonessential loops stop
when offscreen or hidden. Sound never plays without a gesture.

## What to cut

The fade-up on every section. The lift on every card. Parallax layers. Cursor followers and
custom cursors. Animated gradient backgrounds. Glow halos on text. Bouncy easing on UI. A
blinking caret outside a typed command. Auto-rotating carousels. Tab content sliding sideways.
`will-change` set on a whole class instead of during the animation. A loading choreography on
a tool. Any animation that would not be missed.

Before shipping, walk every animation and ask what would happen if it were instant. If the
answer is "nothing", make it instant.
