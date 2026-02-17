---
name: tarot
description: >
  Triggers on "add a carousel", "create a carousel", "tarot carousel", "carousel options",
  "slides", "carousel effect", or any carousel-related work.
version: 1.0.0
---

# Tarot Carousel — Skill Guide

Use this guide when creating, configuring, or scripting a `<tarot-carousel>` instance.

---

## 1. HTML Structure

```html
<tarot-carousel>
  <script type="application/json" data-tarot-options>
    {
      "effect": "carousel",
      "loop": false,
      "slidesPerView": 1,
      "gap": "16px",
      "navigation": {
        "showButtons": true,
        "showPagination": true
      }
    }
  </script>

  <tarot-viewport>
    <tarot-track>
      <tarot-slide><!-- Slide 1 --></tarot-slide>
      <tarot-slide><!-- Slide 2 --></tarot-slide>
      <tarot-slide><!-- Slide 3 --></tarot-slide>
    </tarot-track>
  </tarot-viewport>
</tarot-carousel>
```

**Custom elements:** `tarot-carousel`, `tarot-viewport`, `tarot-track`, `tarot-slide`, `tarot-content`, `tarot-slide-icon`.

### Custom Navigation Buttons

Place buttons inside `<tarot-carousel>` with these data attributes — the carousel auto-binds them:

```html
<button data-action-tarot-previous>Prev</button>
<button data-action-tarot-next>Next</button>
```

Pagination dots use `data-action-tarot-go-to-page`.

Alternatively, use `navigation.previousButtonSelector` / `navigation.nextButtonSelector` options to point at buttons anywhere in the DOM.

---

## 2. Options Reference

All options with defaults. Pass via `<script type="application/json" data-tarot-options>`.

### Core

| Option | Type | Default | Description |
|---|---|---|---|
| `effect` | string | `'carousel'` | Visual effect (see §3) |
| `loop` | boolean | `false` | Infinite looping |
| `slidesPerView` | number | `1` | Visible slides |
| `slidesPerMove` | number | `1` | Slides moved per nav action |
| `gap` | number\|string | `0` | Space between slides (`16` or `'16px'`) |
| `paddingLeft` | number\|string | `0` | Left viewport padding |
| `paddingRight` | number\|string | `0` | Right viewport padding |
| `slideMinWidth` | string | `'50px'` | Minimum slide width |
| `initialIndex` | number | `0` | Starting slide |
| `centerSelectedSlide` | boolean | `false` | Center the active slide |
| `draggable` | boolean | `true` | Enable drag |
| `dragThreshold` | number | `40` | Min px to trigger slide change |
| `focusOnSelect` | boolean | `false` | Click slide to select it |
| `snap` | string | `'page'` | `'page'` \| `'slide'` \| `'none'` |
| `filterClass` | string | `''` | Class to filter included slides |
| `announcements` | boolean | `true` | Screen reader announcements |
| `goToSelectedSlide` | boolean | `false` | Auto-navigate to selected slide |
| `breakpointElement` | string | `'window'` | `'window'` \| `'viewport'` for breakpoints |

### Navigation

```json
{
  "navigation": {
    "showButtons": true,
    "showPreviousButton": true,
    "showNextButton": true,
    "previousButtonSelector": false,
    "nextButtonSelector": false,
    "smartButtons": false,
    "showPagination": true,
    "paginationSelector": false
  }
}
```

### Animation

```json
{
  "animation": {
    "attraction": 0.026,
    "friction": 0.25,
    "speed": 5,
    "velocityBoost": 1.4,
    "freeScrollFriction": 0.96
  }
}
```

### Autoplay

```json
{
  "autoplay": {
    "interval": 0,
    "stopAfterInteraction": true,
    "afterInteraction": "pause"
  }
}
```

`interval` in ms. `0` = disabled. `afterInteraction`: `'stop'` | `'pause'`.

### Sync

| Option | Type | Default | Description |
|---|---|---|---|
| `asNavFor` | string\|false | `false` | Selector — this carousel navigates the target |
| `syncWith` | string\|false | `false` | Selector — sync position with another carousel |

### Breakpoints

Min-width keys. Options merge non-cumulatively (each breakpoint is independent):

```json
{
  "breakpoints": {
    "768": { "slidesPerView": 2, "gap": "20px" },
    "1024": { "slidesPerView": 3, "gap": "24px" },
    "1280": { "slidesPerView": 4, "gap": "28px" }
  }
}
```

---

## 3. Available Effects

| Effect | Import | Notes |
|---|---|---|
| `carousel` | Built-in | Standard horizontal slide. No restrictions. |
| `fade` | Built-in | Cross-fade. Forces `slidesPerView: 1`. Options: `fadeBlur` (default 10), `fadeScale` (default 0.1). |
| `ripple` | `scripts/packages/ripple.js` | Wave with perspective. Sets padding to 0. Best for image galleries. |
| `spotlight` | `scripts/packages/spotlight.js` | Center slide highlighted, sides dimmed/blurred. |
| `sliding-window` | `scripts/packages/sliding-window.js` | Window-like depth effect. Forces `slidesPerView: 1`. |
| `butterfly` | Not imported in Luna | — |
| `fan` | Not imported in Luna | — |
| `cube` | Not imported in Luna | — |
| `peacock` | Not imported in Luna | — |
| `flip` | Not imported in Luna | — |
| `stack` | Not imported in Luna | — |

**Luna imports:** `carousel`, `fade` (built-in) + `ripple`, `spotlight`, `sliding-window` (in `scripts/scripts.js`).

---

## 4. JavaScript API

### Getting a Reference

```javascript
const carousel = document.querySelector('tarot-carousel');
```

### Navigation

```javascript
carousel.next();                      // Next page
carousel.previous();                  // Previous page
carousel.goToSlide(index);            // Animate to slide
carousel.jumpToSlide(index);          // Jump without animation
carousel.goToPage(page);              // Animate to page
carousel.jumpToPage(page);            // Jump to page without animation
carousel.requestTrackPosition('50%'); // Move to track position (number 0–100 or string)
```

### Slide Management

```javascript
carousel.addSlide('<tarot-slide>New slide</tarot-slide>');    // Append slide (HTML string or element)
carousel.addSlide(element, 2);                          // Insert at index
carousel.removeSlide(index);                            // Remove slide
carousel.getSlideAtIndex(index);                        // Get slide element
carousel.setSelectedIndex(index);                       // Select slide
carousel.setSlideState(index, 'active');                // 'active' | 'hidden' | 'disabled'
```

### Properties

```javascript
carousel.index            // Current render index (get/set)
carousel.page             // Current page index
carousel.selectedIndex    // Selected slide index (get/set)
carousel.selectedSlide    // Selected slide element
carousel.slides           // Array of slide descriptors
carousel.options          // Current resolved options
carousel.userOptions      // User-provided options before defaults
carousel.state            // Runtime state snapshot
carousel.viewport         // Viewport element
carousel.track            // Track element
carousel.effects          // Registered effect keys
```

### State Object

```javascript
carousel.state = {
  selectedIndex,   // Currently selected slide
  renderIndex,     // Currently displayed slide
  pageIndex,       // Current page
  pageCount,       // Total pages
  canLoop,         // Whether looping is possible
  isDragging,      // Drag state
  slideCount,      // Total slides
  snapPoints,      // Track positions per page
}
```

### Options

```javascript
carousel.updateOptions({ slidesPerView: 3, gap: '24px' }); // Merge new options
```

### Events

```javascript
carousel.on('render-index:changed', ({ prevIndex, currentIndex }) => { });
carousel.off('render-index:changed', handler);
```

**Key events:**

| Event | Payload | When |
|---|---|---|
| `carousel:ready` | — | Carousel initialized and ready |
| `render-index:changed` | `{ prevIndex, currentIndex }` | Displayed slide changed |
| `selected-index:changed` | `{ prevIndex, currentIndex }` | Selected slide changed |
| `page-index:changed` | — | Page changed |
| `slides:visible-changed` | — | Visible slides changed |
| `drag:start` | `{ event, drag }` | Drag began |
| `drag:end` | `{ event, drag }` | Drag ended |
| `user:interacted` | `{ via, event }` | Any user interaction (`via`: `'hover'`\|`'drag'`\|`'click'`\|`'wheel'`\|`'key'`\|`'focus'`) |
| `movement:completed` | `{ renderIndex, pageIndex, movementType }` | Animation finished |
| `carousel:before-transition` | — | Before transition starts |
| `carousel:after-transition` | — | After transition completes |
| `options:changed` | `{ prevOptions, currentOptions }` | Options updated |
| `state:changed` | `{ prevState, currentState }` | State changed |

### Static Methods

```javascript
Tarot.registerEffect(MyEffect);   // Register custom effect
Tarot.use(MyPlugin);              // Register plugin (chainable)
```

### Lifecycle

```javascript
carousel.destroy(); // Clean up and remove
```

---

## 5. Responsive Breakpoints Example

```json
{
  "slidesPerView": 1,
  "gap": "12px",
  "navigation": { "showButtons": false, "showPagination": true },
  "breakpoints": {
    "640": {
      "slidesPerView": 2,
      "gap": "16px"
    },
    "768": {
      "slidesPerView": 2,
      "gap": "20px",
      "navigation": { "showButtons": true }
    },
    "1024": {
      "slidesPerView": 3,
      "gap": "24px"
    },
    "1280": {
      "slidesPerView": 4,
      "gap": "28px"
    }
  }
}
```

Keys are min-width in pixels. Each breakpoint is a standalone override — they do **not** cascade from smaller breakpoints.
