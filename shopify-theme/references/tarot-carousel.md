# Tarot Carousel Reference

Full configuration reference for the Tarot carousel system. Options sourced from `options-manager.js`.

---

## Web Component Hierarchy

```html
<tarot-carousel>
  <script type="application/json" data-tarot-options>
    { /* configuration JSON */ }
  </script>

  <tarot-viewport>
    <tarot-track>
      <tarot-slide><!-- content --></tarot-slide>
      <tarot-slide><!-- content --></tarot-slide>
    </tarot-track>
  </tarot-viewport>
</tarot-carousel>
```

All elements are custom web components. The `<script>` with `data-tarot-options` must be a direct child of `<tarot-carousel>`.

---

## Core Options

| Option | Type | Default | Description |
|---|---|---|---|
| `effect` | string | `'carousel'` | Visual effect: `carousel`, `fade`, `ripple`, `spotlight`, `sliding-window` |
| `loop` | boolean | `false` | Enable infinite looping |
| `slidesPerView` | number | `1` | Number of slides visible at once |
| `slidesPerMove` | number | `1` | Number of slides to advance on navigation |
| `gap` | number\|string | `0` | Space between slides (px or CSS string like `'16px'`) |
| `paddingLeft` | number\|string | `0` | Left viewport padding (px or CSS string like `'10%'`) |
| `paddingRight` | number\|string | `0` | Right viewport padding |
| `slideMinWidth` | string | `'50px'` | Minimum slide width |
| `initialIndex` | number | `0` | Starting slide index |
| `centerSelectedSlide` | boolean | `false` | Visually center the selected slide |
| `draggable` | boolean | `true` | Whether the carousel can be dragged |
| `dragThreshold` | number | `40` | Minimum drag distance (px) to trigger slide change |
| `focusOnSelect` | boolean | `false` | Whether clicking a slide selects it |
| `snap` | string | `'page'` | Snap behavior: `'page'` (default), `'slide'` (free scroll + snap), `'none'` (free scroll) |
| `filterClass` | string | `''` | Class to filter which slides are included |
| `announcements` | boolean | `true` | Enable screen reader announcements |
| `goToSelectedSlide` | boolean | `false` | Automatically go to the selected slide |
| `breakpointElement` | string | `'window'` | Element for breakpoint measurement: `'window'` or `'viewport'` |

---

## Navigation Options

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

| Option | Type | Default | Description |
|---|---|---|---|
| `showButtons` | boolean | `true` | Show prev/next buttons |
| `showPreviousButton` | boolean | `true` | Show previous button |
| `showNextButton` | boolean | `true` | Show next button |
| `previousButtonSelector` | boolean\|string | `false` | Custom selector for previous button |
| `nextButtonSelector` | boolean\|string | `false` | Custom selector for next button |
| `smartButtons` | boolean | `false` | Hide buttons when at navigation limits |
| `showPagination` | boolean | `true` | Show pagination dots |
| `paginationSelector` | boolean\|string | `false` | Custom selector for pagination container |

### Custom Button Example

```liquid
{%- liquid
  assign id = section.id | split: '_' | last
  assign id = "section-" | append: id
-%}

<tarot-carousel>
  <script type="application/json" data-tarot-options>
    {
      "navigation": {
        "showButtons": true,
        "showPagination": false,
        "previousButtonSelector": "#prev-{{ id }}",
        "nextButtonSelector": "#next-{{ id }}"
      }
    }
  </script>
  <tarot-viewport>
    <tarot-track>...</tarot-track>
  </tarot-viewport>
</tarot-carousel>

<button id="prev-{{ id }}" class="tarot-button tarot-prev" data-action="tarot-prev">
  {% render 'icon', icon: 'chevron-left' %}
</button>
<button id="next-{{ id }}" class="tarot-button tarot-next" data-action="tarot-next">
  {% render 'icon', icon: 'chevron-right' %}
</button>
```

---

## Animation Options

Physics-based spring animation:

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

| Option | Type | Default | Description |
|---|---|---|---|
| `attraction` | number | `0.026` | Spring attraction coefficient |
| `friction` | number | `0.25` | Friction coefficient for dampening |
| `speed` | number | `5` | Base animation speed |
| `velocityBoost` | number | `1.4` | Multiplier for initial velocity |
| `freeScrollFriction` | number | `0.96` | Friction for free scroll momentum (0–1, higher = more slippery) |

---

## Autoplay Options

```json
{
  "autoplay": {
    "interval": 5000,
    "stopAfterInteraction": true,
    "afterInteraction": "pause"
  }
}
```

| Option | Type | Default | Description |
|---|---|---|---|
| `interval` | number | `0` | Time between slides in ms (0 = disabled) |
| `stopAfterInteraction` | boolean | `true` | Stop autoplay after user interaction |
| `afterInteraction` | string | `'pause'` | Behavior after interaction: `'stop'` or `'pause'` |

---

## Sync Options

| Option | Type | Default | Description |
|---|---|---|---|
| `asNavFor` | boolean\|string | `false` | Selector for carousel to sync navigation with |
| `syncWith` | boolean\|string | `false` | Selector for carousel to sync slide position with |

---

## Responsive Breakpoints

Breakpoints are **non-cumulative** — only the single matching breakpoint is merged with the base options.

```json
{
  "slidesPerView": 1,
  "gap": "12px",
  "breakpoints": {
    "768": {
      "slidesPerView": 2,
      "slidesPerMove": 1,
      "gap": "20px"
    },
    "1024": {
      "slidesPerView": 3,
      "slidesPerMove": 2,
      "gap": "24px"
    },
    "1280": {
      "slidesPerView": 4,
      "slidesPerMove": 3,
      "gap": "28px"
    }
  }
}
```

Breakpoints use min-width matching against `window.innerWidth` by default (or viewport element width when `breakpointElement: 'viewport'`). The largest breakpoint `<=` current width wins.

---

## Available Effects

Effects imported in `scripts/scripts.js`:

| Effect | Import | Notes |
|---|---|---|
| `carousel` | Built-in (core) | Standard horizontal sliding |
| `fade` | Built-in (core) | Smooth fade transitions; forces `slidesPerView: 1` |
| `ripple` | `./packages/ripple.js` | Dynamic wave with perspective; auto-sets padding to 0 |
| `spotlight` | `./packages/spotlight.js` | Center slide highlighted, surrounding dimmed; best with odd slidesPerView |
| `sliding-window` | `./packages/sliding-window.js` | Window-like sliding with depth/perspective |

---

## Liquid Integration Pattern

### Settings Extraction

```liquid
{%- liquid
  assign s = section.settings
  assign effect = s.carousel_effect | default: 'ripple'
  assign loop_enabled = s.carousel_loop | default: false
  assign gap_value = s.carousel_gap | default: 16
  assign gap_string = gap_value | append: 'px'
  assign padding_left_mobile = s.carousel_padding_left_mobile | default: 0
  assign padding_right_mobile = s.carousel_padding_right_mobile | default: 0
  assign padding_left_tablet = s.carousel_padding_left_tablet | default: padding_left_mobile
  assign padding_right_tablet = s.carousel_padding_right_tablet | default: padding_right_mobile
-%}
```

### JSON Config with Liquid Filters

Always use `| json` for Liquid values inside JSON:

```liquid
<script type="application/json" data-tarot-options>
  {
    "effect": {{ effect | json }},
    "loop": {{ loop_enabled | json }},
    "gap": {{ gap_string | json }},
    "paddingLeft": {{ padding_left_mobile | json }},
    "paddingRight": {{ padding_right_mobile | json }},
    "slidesPerView": 1,
    "navigation": {
      "showButtons": false,
      "showPagination": false
    },
    "breakpoints": {
      "768": {
        "slidesPerView": 2,
        "slidesPerMove": 1,
        "paddingLeft": {{ padding_left_tablet | json }},
        "paddingRight": {{ padding_right_tablet | json }}
      },
      "1024": {
        "slidesPerView": 3,
        "slidesPerMove": 2
      }
    }
  }
</script>
```

---

## Schema Settings Pattern

Add these to a section schema for carousel configuration:

```json
{
  "type": "header",
  "content": "Carousel"
},
{
  "type": "select",
  "id": "carousel_effect",
  "label": "Effect",
  "default": "carousel",
  "options": [
    { "value": "carousel", "label": "Carousel" },
    { "value": "fade", "label": "Fade" },
    { "value": "ripple", "label": "Ripple" },
    { "value": "spotlight", "label": "Spotlight" },
    { "value": "sliding-window", "label": "Sliding Window" }
  ]
},
{
  "type": "checkbox",
  "id": "carousel_loop",
  "label": "Loop",
  "default": false
},
{
  "type": "range",
  "id": "carousel_gap",
  "label": "Gap",
  "min": 0,
  "max": 48,
  "step": 4,
  "unit": "px",
  "default": 16
}
```

For padding controls, add per-breakpoint settings:
```json
{
  "type": "range",
  "id": "carousel_padding_left_mobile",
  "label": "Padding Left (Mobile)",
  "min": 0,
  "max": 100,
  "step": 4,
  "unit": "px",
  "default": 0
}
```

---

## Complete Section Example

Based on `collection-list-carousel.liquid`:

```liquid
<!-- section: my-carousel -->

{%- liquid
  assign s = section.settings
  assign effect = s.carousel_effect | default: 'ripple'
  assign loop_enabled = s.carousel_loop | default: false
  assign gap_string = s.carousel_gap | default: 16 | append: 'px'
-%}

<section class="py-section color-{{ s.color_scheme }}">
  <div class="container">

    {% if s.heading != blank %}
      <h2 data-aos="fade-up" class="h3 mb-8">{{ s.heading }}</h2>
    {% endif %}

    <tarot-carousel>
      <script type="application/json" data-tarot-options>
        {
          "effect": {{ effect | json }},
          "loop": {{ loop_enabled | json }},
          "gap": {{ gap_string | json }},
          "slidesPerView": 1,
          "navigation": {
            "showButtons": false,
            "showPagination": true
          },
          "breakpoints": {
            "768": {
              "slidesPerView": 2,
              "slidesPerMove": 1
            },
            "1024": {
              "slidesPerView": 3,
              "slidesPerMove": 2
            },
            "1280": {
              "slidesPerView": 4,
              "slidesPerMove": 3
            }
          }
        }
      </script>

      <tarot-viewport>
        <tarot-track>
          {% for block in section.blocks %}
            <tarot-slide {{ block.shopify_attributes }}>
              {%- liquid
                assign b = block.settings
              -%}
              {% render 'image',
                image: b.image,
                class: 'w-full aspect-product-card object-cover',
                placeholder: 'collection',
                index: forloop.index %}
              <h3 class="fs-18 mt-4">{{ b.heading }}</h3>
            </tarot-slide>
          {% endfor %}
        </tarot-track>
      </tarot-viewport>
    </tarot-carousel>

  </div>
</section>

{% schema %}
{
  "name": "My Carousel",
  "settings": [
    {
      "type": "color_scheme",
      "id": "color_scheme",
      "label": "Color scheme"
    },
    {
      "type": "textarea",
      "id": "heading",
      "label": "Heading"
    },
    {
      "type": "header",
      "content": "Carousel"
    },
    {
      "type": "select",
      "id": "carousel_effect",
      "label": "Effect",
      "default": "carousel",
      "options": [
        { "value": "carousel", "label": "Carousel" },
        { "value": "fade", "label": "Fade" },
        { "value": "ripple", "label": "Ripple" },
        { "value": "spotlight", "label": "Spotlight" },
        { "value": "sliding-window", "label": "Sliding Window" }
      ]
    },
    {
      "type": "checkbox",
      "id": "carousel_loop",
      "label": "Loop",
      "default": false
    },
    {
      "type": "range",
      "id": "carousel_gap",
      "label": "Gap",
      "min": 0,
      "max": 48,
      "step": 4,
      "unit": "px",
      "default": 16
    }
  ],
  "blocks": [
    {
      "type": "slide",
      "name": "Slide",
      "settings": [
        {
          "type": "image_picker",
          "id": "image",
          "label": "Image"
        },
        {
          "type": "text",
          "id": "heading",
          "label": "Heading"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "My Carousel",
      "category": "Media & Content"
    }
  ],
  "disabled_on": {
    "groups": ["*"]
  }
}
{% endschema %}
```

---

## Performance Tips

- Use `loading="lazy"` on images in non-visible slides; `loading="eager"` only for the first slide
- Standard `carousel` effect has the best performance
- Consider pagination for large slide counts
- Use Luna's `image.liquid` snippet for responsive srcset generation

---

## Source Files

- `scripts/packages/tarot.esm.js` — Core carousel (carousel + fade effects)
- `scripts/packages/ripple.js` — Ripple effect plugin
- `scripts/packages/spotlight.js` — Spotlight effect plugin
- `scripts/packages/sliding-window.js` — Sliding window effect plugin
- `@magic-spells/tarot/src/scripts/core/options-manager.js` — Canonical options definition
