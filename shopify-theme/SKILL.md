---
name: shopify-theme
description: >
  Triggers on "create a section", "build a snippet", "add a carousel", "edit a Liquid file",
  "add a block", "build a Shopify template", or any Luna/Foster White theme work.
version: 1.0.0
---

# Luna Shopify Theme — Skill Guide

Use this guide whenever creating or editing Liquid sections, snippets, templates, or JS components in the Luna theme.

---

## 1. File Labels

Section and snippet Liquid file starts with an identifying comment:

```liquid
<!-- section: section-name -->
<!-- snippet: snippet-name -->
```

---

## 2. Section Anatomy

Follow this skeleton for every new section:

```liquid
<!-- section: my-section -->

{%- liquid
  assign s = section.settings
-%}

<section class="relative py-section color-{{ s.color_scheme }}">
  <div class="container">

    {% if s.overline != blank %}
      <p data-aos="fade-in-up" class="overline-heading">
        {{ s.overline }}
      </p>
    {% endif %}

    {% if s.heading != blank %}
      <h2
        data-aos="fade-up"
        data-aos-delay="100"
        class="max-w-[1000px] mx-auto mb-6 text-pretty">
        {{ s.heading }}
      </h2>
    {% endif %}

    {% #  Block loop  %}
    {% for block in section.blocks %}
      {%- assign b = block.settings -%}
      <div {{ block.shopify_attributes }}>
        {{ b.text }}
      </div>
    {% endfor %}

    {% #  Button  %}
    {% if s.link %}
      <div data-aos="fade-up" data-aos-delay="200">
        <a class="button {{ s.button_color }} {{ s.button_style }}"
          href="{{ s.link }}">
          {{ s.button_text }}
        </a>
      </div>
    {% endif %}

  </div>
</section>


{% schema %}
{
  "name": "My Section",
  "settings": [
    {
      "type": "color_scheme",
      "id": "color_scheme",
      "label": "Color scheme"
    },
    {
      "type": "header",
      "content": "Content"
    },
    {
      "type": "text",
      "id": "overline",
      "label": "Overline"
    },
    {
      "type": "textarea",
      "id": "heading",
      "label": "Heading"
    },
    {
      "type": "header",
      "content": "Button"
    },
    {
      "type": "url",
      "id": "link",
      "label": "Link"
    },
    {
      "type": "text",
      "id": "button_text",
      "label": "Button text",
      "default": "Shop now"
    },
    {
      "type": "select",
      "id": "button_style",
      "label": "Button style",
      "options": [
        { "value": "button-solid", "label": "Solid" },
        { "value": "button-outline", "label": "Outline" },
        { "value": "button-transparent", "label": "Transparent" },
        { "value": "button-text", "label": "Text" }
      ],
      "default": "button-outline"
    },
    {
      "type": "select",
      "id": "button_color",
      "label": "Button color",
      "options": [
        { "value": "button-primary", "label": "Primary" },
        { "value": "button-secondary", "label": "Secondary" },
        { "value": "button-white", "label": "White" },
        { "value": "button-black", "label": "Black" }
      ],
      "default": "button-primary"
    }
  ],
  "blocks": [],
  "presets": [
    {
      "name": "My Section",
      "category": "Media & Content"
    }
  ],
  "disabled_on": {
    "groups": ["*"]
  }
}
{% endschema %}
```

**Key conventions:**

- `assign s = section.settings` — always unwrap at top
- `assign b = block.settings` — always unwrap inside block loops
- `{{ block.shopify_attributes }}` on every block wrapper element
- `color-{{ s.color_scheme }}` on the root `<section>`
- `py-section` for vertical section padding
- `container` for centered max-width content
- `color_scheme` is always the first setting in the schema
- Use `header` settings to group related schema options
- `presets` with a `category` for the theme editor
- `{% #  Block loop  %}` - single line Liquid comments use hash tag like this
  - DO NOT try to invent new styles of comments that don't exist! DO NOT do `{%# Text #}` because that isn't a real Liquid comment! Double check your inline comments for accuracy!!
- `disabled_on: { groups: ["*"] }` to prevent section from being added to groups

---

## 2b. Section Padding Pattern

Sections use `py-section` (or `pt-section` / `pb-section`) for vertical padding. When adding optional padding removal, **always build the class string in the Liquid logic block at the top** — never use inline `{% unless %}` logic inside HTML class attributes.

**DO THIS** — clean, readable:

```liquid
{%- liquid
  assign s = section.settings

  assign padding_class = ''
  unless s.remove_top_padding
    assign padding_class = 'pt-section'
  endunless
  unless s.remove_bottom_padding
    assign padding_class = padding_class | append: ' pb-section'
  endunless
-%}

<section class="color-{{ s.color_scheme }}">
  <div class="{{ padding_class }}">
    ...
  </div>
</section>
```

**DON'T DO THIS** — inline conditional logic in class attributes is ugly and hard to read:

```liquid
{%- # BAD — don't do this -%}
<div class="{% unless s.remove_top_padding %}pt-section{% endunless %} {% unless s.remove_bottom_padding %}pb-section{% endunless %}">
```

**Schema settings:**

```json
{
  "type": "header",
  "content": "Section padding"
},
{
  "type": "checkbox",
  "id": "remove_top_padding",
  "label": "Remove top padding",
  "default": false
},
{
  "type": "checkbox",
  "id": "remove_bottom_padding",
  "label": "Remove bottom padding",
  "default": false
}
```

This pattern applies generally: keep conditional class-building logic in the `{%- liquid %}` block at the top of the section, then output clean `{{ variable }}` references in the HTML.

---

## 3. Snippet Anatomy

Snippets use a `{% doc %}` block instead of a schema:

```liquid
<!-- snippet: my-snippet -->

{% doc %}
  Brief description of what this snippet renders.

  @param {object} image - Shopify image object (required)
  @param {string} [class] - CSS classes (optional)
  @param {string} [loading] - 'eager' or 'lazy' (default: 'lazy')

  @example
  {% render 'my-snippet',
    image: product.featured_image,
    class: 'w-full' %}
{% enddoc %}

{%- liquid
  assign loading = loading | default: "lazy"
-%}

<div class="{{ class }}">
  {{ image | image_url: width: 800 }}
</div>
```

---

## 4. Image Rendering

**Single image** — `image.liquid`:

```liquid
{% render 'image',
  image: s.image,
  sizes: '50vw',
  widths: '400, 600, 800, 1200',
  class: 'w-full h-full object-cover',
  loading: 'eager',
  placeholder: 'product',
  index: forloop.index,
  aos: 'fade-in' %}
```

**Responsive mobile/desktop** — `picture.liquid`:

```liquid
{% render 'picture',
  mobile_image: s.mobile_image,
  desktop_image: s.desktop_image,
  class: 'w-full h-100vh object-cover',
  picture_class: 'block',
  loading: 'eager',
  placeholder: 'lifestyle',
  index: forloop.index %}
```

Placeholder types: `product`, `collection`, `lifestyle`, `image`

---

## 5. Button Pattern

**Schema settings** (always in pairs — style + color):

```json
{
  "type": "select",
  "id": "button_style",
  "label": "Button style",
  "options": [
    { "value": "button-solid", "label": "Solid" },
    { "value": "button-outline", "label": "Outline" },
    { "value": "button-transparent", "label": "Transparent" },
    { "value": "button-text", "label": "Text" }
  ],
  "default": "button-outline"
},
{
  "type": "select",
  "id": "button_color",
  "label": "Button color",
  "options": [
    { "value": "button-primary", "label": "Primary" },
    { "value": "button-secondary", "label": "Secondary" },
    { "value": "button-white", "label": "White" },
    { "value": "button-black", "label": "Black" }
  ],
  "default": "button-primary"
}
```

**Render:**

```liquid
<a class="button {{ s.button_color }} {{ s.button_style }}" href="{{ s.link }}">
  {{ s.button_text }}
</a>
```

Additional variants: `button-tonal`, `button-small`

---

## 6. Color Scheme

Apply on the section root: `color-{{ s.color_scheme }}`

Available utility classes within a color scheme context:

| Class                     | Purpose                          |
| ------------------------- | -------------------------------- |
| `bg-scheme`               | Background with gradient support |
| `text-scheme`             | Foreground text color            |
| `bg-scheme-fg`            | Foreground color as background   |
| `text-scheme-bg`          | Background color as text         |
| `border-fg` / `border-bg` | Border colors                    |
| `text-scheme-links`       | Link color                       |
| `text-scheme-icons`       | Icon color                       |

---

## 7. Key Utilities (Quick Ref)

| Utility                                                                     | What it does                                               |
| --------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `fs-{n}`                                                                    | Font size in px → rem (e.g. `fs-18` = 18/16 rem)           |
| `fw-{n}`                                                                    | Font weight (e.g. `fw-400`, `fw-700`)                      |
| `font-body` / `font-heading` / `font-subheading` / `font-accent`            | Theme font families                                        |
| `container`                                                                 | Max-width + gutter padding                                 |
| `flex-center`                                                               | `display:flex; align-items:center; justify-content:center` |
| `absolute-fill`                                                             | `position:absolute; inset:0; width:100%; height:100%`      |
| `center-x` / `center-y` / `center-xy`                                       | Absolute centering via transform                           |
| `py-section` / `pt-section` / `pb-section`                                  | Responsive section padding                                 |
| `transition-fast`                                                           | 200ms ease-in-out                                          |
| `transition-medium`                                                         | 400ms ease-in-out                                          |
| `transition-slow`                                                           | 700ms ease-in-out                                          |
| `z-header` (100) / `z-cart` (200) / `z-modal` (300)                         | Z-index layers                                             |
| `hide-scrollbar`                                                            | Hides scrollbar cross-browser                              |
| `w-slide`                                                                   | Tarot slide width variable                                 |
| `aspect-product-card`                                                       | Product thumbnail aspect ratio                             |
| `rounded-inputs` / `rounded-buttons` / `rounded-theme` / `rounded-theme-sm` | Theme border radii                                         |
| `h-100vh` / `h-100svh` / `h-100dvh` / `h-hero`                              | Height utilities                                           |
| `overline-heading`                                                          | Overline typography preset                                 |
| `.h1`–`.h6`                                                                 | Heading typography presets                                 |

**Breakpoints:** sm(576) · md(768) · mde(896) · lg(1024) · xl(1280) · 2xl(1536) · 3xl(1920)

**Spacing scale (responsive):** `spacing-xxs` · `spacing-xs` · `spacing-sm` · `spacing-md` · `spacing-lg` · `spacing-xl` · `spacing-2xl` · `spacing-3xl`

See [references/tailwind-utilities.md](references/tailwind-utilities.md) for complete details.

---

## 8. AOS Animations

Common animations used in sections:

```liquid
data-aos="fade-in"          {% comment %} Simple fade {% endcomment %}
data-aos="fade-in-up"       {% comment %} Fade + slide up (overlines) {% endcomment %}
data-aos="fade-up"          {% comment %} Slide up (headings, content) {% endcomment %}
data-aos="zoom-in-up"       {% comment %} Zoom + slide up {% endcomment %}
```

**Staggered delays** — increment by 100ms per element:

```liquid
data-aos="fade-up" data-aos-delay="100"
data-aos="fade-up" data-aos-delay="200"
data-aos="fade-up" data-aos-delay="300"
```

**Anchoring** — tie animation triggers to a parent:

```liquid
data-aos="fade-up" data-aos-anchor="#{{ id }}"
```

The `image.liquid` and `picture.liquid` snippets accept an `aos` param for image animations.

---

## 9. Tarot Carousel (Quick Start)

```html
<tarot-carousel>
  <script type="application/json" data-tarot-options>
    {
      "effect": "carousel",
      "loop": true,
      "gap": "16px",
      "slidesPerView": 1,
      "navigation": {
        "showButtons": false,
        "showPagination": true
      },
      "breakpoints": {
        "768": { "slidesPerView": 2, "gap": "20px" },
        "1024": { "slidesPerView": 3, "gap": "24px" }
      }
    }
  </script>

  <tarot-viewport>
    <tarot-track>
      <tarot-slide><!-- content --></tarot-slide>
    </tarot-track>
  </tarot-viewport>
</tarot-carousel>
```

**Available effects:** `carousel`, `fade`, `ripple`, `spotlight`, `sliding-window`

**Liquid settings integration:**

```liquid
{%- liquid
  assign effect = s.carousel_effect | default: 'carousel'
  assign loop_enabled = s.carousel_loop | default: false
  assign gap_string = s.carousel_gap | default: 16 | append: 'px'
-%}
"effect": {{ effect | json }},
"loop": {{ loop_enabled | json }},
"gap": {{ gap_string | json }},
```

See [references/tarot-carousel.md](references/tarot-carousel.md) for all options.

---

## 10. Data-Attribute System

The theme uses data attributes (not classes) for JavaScript bindings:

### `data-action-*` — Event triggers

Elements that fire JS actions on click. Handled via document-level event delegation with `e.target.closest()`:

```html
<button data-action-show-mobile-menu>Menu</button>
<button data-action-add-variant-to-cart data-variant="{{ variant.id }}">
  Add
</button>
<button data-action-show-cart-panel>Cart</button>
<button data-action-play-video>Play</button>
```

```javascript
document.addEventListener("click", (e) => {
  const trigger = e.target.closest("[data-action-show-mobile-menu]");
  if (!trigger) return;
  e.preventDefault();
  mobileMenu.show();
});
```

### `data-content-*` — Dynamic content targets

Elements whose `innerHTML` or `textContent` gets swapped:

```html
<span data-content="product-price">$49.00</span>
<span data-content="product-compare-price">$59.00</span>
```

### `data-component-*` — Component roots

Wrappers that scope child element queries:

```html
<div data-component-shopify-video>
  <button data-action-play-video>Play</button>
  <div data-video-overlay>...</div>
  <video>...</video>
</div>
```

### Direct data attributes — Element references

Named identifiers instead of IDs or classes. Use descriptive kebab-case names:

```html
<form data-product-form>
  <button data-add-to-cart-button>
    <div data-video-overlay>
      <div data-sticky-add-to-cart-panel>
        <header data-scrolling="false">
          <input data-product-option />
        </header>
      </div>
    </div>
  </button>
</form>
```

### `data-state="value"` — State tracking

Tracks element state for CSS and JS:

```html
<button data-add-to-cart-button data-state="">Add to Cart</button>
<button data-add-to-cart-button data-state="out-of-stock" disabled>
  Sold Out
</button>
```

### Always pair with ARIA

Data attributes are always paired with ARIA for accessibility:

```javascript
mobileMenu.setAttribute("aria-hidden", "false");
menuButton.setAttribute("aria-expanded", "true");
option.setAttribute("aria-checked", "true");
```

---

## 11. Web Components (Quick Start)

The theme uses Magic Spells web component packages:

| Package                             | Element                 | Purpose                 |
| ----------------------------------- | ----------------------- | ----------------------- |
| `@magic-spells/bottom-sheet`        | `<bottom-sheet>`        | Mobile bottom sheets    |
| `@magic-spells/cart-progress-bar`   | `<cart-progress-bar>`   | Free shipping progress  |
| `@magic-spells/collapsible-content` | `<collapsible-content>` | Accordion panels        |
| `@magic-spells/dialog-panel`        | `<dialog-panel>`        | Modal dialogs           |
| `@magic-spells/dropdown-panel`      | `<dropdown-panel>`      | Dropdown menus          |
| `@magic-spells/dropdown-select`     | `<dropdown-select>`     | Custom selects          |
| `@magic-spells/load-content`        | `<load-content>`        | Lazy-loaded content     |
| `@magic-spells/scrolling-content`   | `<scrolling-content>`   | Scroll-based content    |
| `@magic-spells/scroll-progress`     | `<scroll-progress>`     | Scroll progress bars    |
| `@magic-spells/tab-group`           | `<tab-group>`           | Tabbed interfaces       |
| `@magic-spells/quantity-input`      | `<quantity-input>`      | +/- quantity fields     |
| `@magic-spells/responsive-video`    | `<responsive-video>`    | Responsive video embeds |

**Custom web component pattern:**

```javascript
class MyComponent extends HTMLElement {
  constructor() {
    super();
    const _ = this;
    // init properties
  }

  connectedCallback() {
    const _ = this;
    _.queryElements();
    _.attachListeners();
  }

  disconnectedCallback() {
    const _ = this;
    // clean up listeners
  }
}
customElements.define("my-component", MyComponent);
```

**Custom events** use the `theme:` namespace:

```javascript
_.dispatchEvent(
  new CustomEvent("theme:variant-selector:change", {
    detail: { index, value },
    bubbles: true,
  }),
);
```

**Global data** lives on `window.Theme`:

- `window.Theme.product` — current product object
- `window.Theme.currency.format` — currency format string

See [references/web-components.md](references/web-components.md) for full patterns.

---

## 12. Additional Resources

- [Tailwind Utilities Reference](references/tailwind-utilities.md) — All custom utilities, spacing, colors, typography
- [Tarot Carousel Reference](references/tarot-carousel.md) — Full config options, effects, responsive breakpoints
- [Web Components Reference](references/web-components.md) — Magic Spells packages, JS patterns, data-attribute system
