# Web Components & JS Patterns Reference

Magic Spells packages, custom web components, data-attribute system, and JavaScript architecture for the Luna theme.

---

## Magic Spells Packages

Imported in `scripts/scripts.js`:

| Package | Element | Purpose |
|---|---|---|
| `@magic-spells/bottom-sheet` | `<bottom-sheet>` | Mobile bottom sheets |
| `@magic-spells/cart-progress-bar` | `<cart-progress-bar>` | Free shipping progress indicator |
| `@magic-spells/collapsible-content` | `<collapsible-content>` | Accordion/collapsible panels |
| `@magic-spells/dialog-panel` | `<dialog-panel>` | Modal dialogs (search, quickview) |
| `@magic-spells/dropdown-panel` | `<dropdown-panel>` | Dropdown menu panels |
| `@magic-spells/dropdown-select` | `<dropdown-select>` | Custom select dropdowns |
| `@magic-spells/load-content` | `<load-content>` | Lazy-loaded content sections |
| `@magic-spells/scrolling-content` | `<scrolling-content>` | Scroll-based content areas |
| `@magic-spells/scroll-progress` | `<scroll-progress>` | Scroll progress bars |
| `@magic-spells/tab-group` | `<tab-group>` | Tabbed content interfaces |
| `@magic-spells/quantity-input` | `<quantity-input>` | Increment/decrement quantity fields |
| `@magic-spells/responsive-video` | `<responsive-video>` | Responsive video embeds |
| `@magic-spells/cart-panel` | `CartPanel`, `CartItem` | Cart drawer (JS API, not element import) |

### quantity-input Usage

```html
<quantity-input min="1" value="1" class="mt-auto">
  <button data-action-decrement type="button">-</button>
  <input type="number" name="quantity" />
  <button data-action-increment type="button">+</button>
</quantity-input>
```

### dialog-panel Usage

```javascript
const searchDialog = document.querySelector('#search-dialog');
searchDialog.show(trigger);  // pass the triggering element for focus return
```

### cart-panel Usage

```javascript
import { CartPanel, CartItem } from '@magic-spells/cart-panel';

// Set template for cart items
CartItem.setTemplate('default', (itemData, cartData) => {
  return `<div class="cart-item">...</div>`;
});

// Show the cart panel
const cartPanel = document.querySelector('cart-panel');
cartPanel.show(trigger);
```

---

## Data-Attribute System (Detailed)

### `data-action-*` — Event Triggers

Elements that fire JS actions. Handled via document-level event delegation using `e.target.closest()`.

**Two naming conventions exist:**
1. `data-action="action-name"` — value-based (most components)
2. `data-action-action-name` — attribute-based (cart, search, video, quantity)

#### All Known Actions

| Attribute | Component | Action |
|---|---|---|
| `data-action="show-mobile-menu"` | mobile-menu | Open mobile menu |
| `data-action="hide-mobile-menu"` | mobile-menu | Close mobile menu |
| `data-action="segue-to-mobile-nav"` | mobile-menu | Navigate to submenu |
| `data-action="segue-back"` | mobile-menu | Go back in menu |
| `data-action="add-variant-to-cart"` | product-form | Quick add variant (uses `data-variant` attr) |
| `data-action="trigger-add-to-cart-form"` | sticky-add-to-cart | Trigger the main form's add button |
| `data-action="search-input"` | search | Search input field identifier |
| `data-action-show-cart-panel` | cart | Show cart drawer |
| `data-action-show-search-panel` | search | Show search dialog |
| `data-action-play-video` | video | Play video overlay |
| `data-action-decrement` | quantity-input | Decrement quantity |
| `data-action-increment` | quantity-input | Increment quantity |
| `data-action-remove-item` | cart | Remove cart line item |

#### Event Delegation Pattern

```javascript
// Document-level delegation (most common)
document.addEventListener('click', (e) => {
  const trigger = e.target.closest('[data-action="show-mobile-menu"]');
  if (!trigger) return;
  e.preventDefault();
  mobileMenu.show();
});

// Multiple actions in one listener
document.addEventListener('click', (e) => {
  const showEl = e.target.closest('[data-action="show-mobile-menu"]');
  const hideEl = e.target.closest('[data-action="hide-mobile-menu"]');
  const segueEl = e.target.closest('[data-action="segue-to-mobile-nav"]');
  const backEl = e.target.closest('[data-action="segue-back"]');

  if (showEl) { e.preventDefault(); mobileMenu.show(); return; }
  if (hideEl) { e.preventDefault(); mobileMenu.hide(); return; }
  // ...
});
```

---

### `data-content-*` — Content Swap Targets

Elements whose `innerHTML` or `textContent` gets dynamically updated:

| Attribute | Component | Content |
|---|---|---|
| `data-content="product-price"` | product-form | Current variant price |
| `data-content="product-compare-price"` | product-form | Compare-at price |
| `data-content="product-sub-price"` | purchase-type | Subscription price |
| `data-content="subscription-frequency"` | purchase-type | Frequency text |

#### Update Pattern

```javascript
updatePrice(variant) {
  const priceEls = document.querySelectorAll('[data-content="product-price"]');
  priceEls.forEach(el => {
    el.innerHTML = formattedPrice;
  });
}
```

---

### `data-component-*` — Component Roots

Wrapper elements that scope child queries:

| Attribute | Component | Children |
|---|---|---|
| `data-component-shopify-video` | video | `[data-action-play-video]`, `[data-video-overlay]`, `<video>` |

#### Scoped Query Pattern

```javascript
document.addEventListener('click', (e) => {
  const playButton = e.target.closest('[data-action-play-video]');
  if (!playButton) return;

  const component = playButton.closest('[data-component-shopify-video]');
  const video = component.querySelector('video');
  const overlay = component.querySelector('[data-video-overlay]');
  // ...
});
```

---

### Direct Data Attributes — Element References

Named identifiers using descriptive kebab-case. Used instead of IDs or CSS class selectors:

| Attribute | Purpose |
|---|---|
| `data-product-form` | Product form wrapper |
| `data-add-to-cart-button` | Add-to-cart button |
| `data-main-product-form` | Main product form reference |
| `data-product-option` | Variant select input |
| `data-product-option-radio` | Variant radio input |
| `data-video-overlay` | Video overlay element |
| `data-sticky-add-to-cart-panel` | Sticky ATC panel |
| `data-nav-handle` | Navigation section identifier |
| `data-focus` | Keyboard focus target |
| `data-scrolling` | Header scroll state (`"true"` / `"false"`) |
| `data-option-index` | Variant option index number |
| `data-frequency-selector` | Subscription frequency selector |
| `data-variant` | Variant ID on quick-add buttons |
| `data-cart-has-items` | Cart state indicator |

#### Query Pattern

```javascript
connectedCallback() {
  const _ = this;
  _.productForm = _.querySelector('[data-product-form]');
  _.addToCartButton = _.productForm.querySelector('[data-add-to-cart-button]');
}
```

---

### `data-state="value"` — State Tracking

Tracks element state for both CSS styling and JS logic:

| Element | States | Purpose |
|---|---|---|
| `[data-add-to-cart-button]` | `""`, `"out-of-stock"` | ATC button availability |
| `[data-scrolling]` | `"true"`, `"false"` | Header scroll state |

#### State Management Pattern

```javascript
setAddToCartState(state) {
  _.addToCartButton.setAttribute('data-state', state);
}

// Usage
if (variant.available) {
  _.addToCartButton.disabled = false;
  _.setAddToCartState('');
} else {
  _.addToCartButton.disabled = true;
  _.setAddToCartState('out-of-stock');
}
```

---

### ARIA State Management

ARIA attributes serve dual duty — accessibility AND visual state management. Always pair with data attributes:

| Attribute | Usage | Components |
|---|---|---|
| `aria-expanded` | Toggle open/closed state | mobile-menu button |
| `aria-hidden` | Show/hide elements | mobile-menu, sticky-atc, search |
| `aria-checked` | Radio/checkbox selection | purchase-type options |
| `aria-disabled` | Disabled state | purchase-type subscription |
| `aria-live="polite"` | Live region updates | search results |
| `role="listbox"` | Semantic role | search results list |

#### Pattern

```javascript
show() {
  _.menuButton.setAttribute('aria-expanded', 'true');
  _.mobileMenu.setAttribute('aria-hidden', 'false');
  document.body.style.overflow = 'hidden';
}

hide() {
  _.menuButton.setAttribute('aria-expanded', 'false');
  _.mobileMenu.setAttribute('aria-hidden', 'true');
  document.body.style.overflow = '';
}
```

---

## Custom Events

Events use the `theme:` namespace and bubble up the DOM:

| Event | Dispatched by | Detail |
|---|---|---|
| `theme:variant-selector:change` | `<variant-selector>` | `{ index, value }` |
| `theme:purchase-type:change` | `<purchase-type>` | `{ type, selling_plan_id, isSubscription }` |

#### Dispatching

```javascript
_.dispatchEvent(new CustomEvent('theme:variant-selector:change', {
  detail: { index: _.index, value },
  bubbles: true,
}));
```

#### Listening

```javascript
_.addEventListener('theme:variant-selector:change', (e) => {
  _.syncVariantUI(_.calculateVariant());
});

_.addEventListener('theme:purchase-type:change', (e) => {
  _.handlePurchaseTypeChange(e.detail);
});
```

---

## Custom Web Component Pattern

All theme components follow this structure:

```javascript
class MyComponent extends HTMLElement {
  constructor() {
    super();
    const _ = this;
    // Initialize properties
    _.someElement = null;
  }

  connectedCallback() {
    const _ = this;
    _.queryElements();
    _.attachListeners();
    _.initialSync();
  }

  disconnectedCallback() {
    const _ = this;
    // Clean up event listeners
  }

  queryElements() {
    const _ = this;
    _.someElement = _.querySelector('[data-some-element]');
  }

  attachListeners() {
    const _ = this;
    _.addEventListener('click', (e) => { /* ... */ });
  }
}
customElements.define('my-component', MyComponent);
```

**Conventions:**
- Use `const _ = this;` at the start of every method
- Query elements with data attributes, not classes or IDs
- Clean up listeners in `disconnectedCallback()`
- Use `customElements.define()` at file bottom

---

## Cart API

From `scripts/api/shopify-cart.js`:

```javascript
import { addVariantToCart, addFormToCart } from '../api/shopify-cart.js';

// Add single variant
addVariantToCart(variantId, quantity, properties).then((cartData) => {
  cartPanel.show();
});

// Add from form element
addFormToCart(formElement).then((cartData) => {
  cartPanel.show();
});
```

Both functions POST to `/cart/add.json` with `Content-Type: application/json`.

---

## Global `window.Theme`

Theme data accessible globally:

| Property | Type | Purpose |
|---|---|---|
| `window.Theme.product` | object | Current product data (variants, options, etc.) |
| `window.Theme.product.variants` | array | Product variants |
| `window.Theme.product.selling_plan_groups` | array | Subscription plan groups |
| `window.Theme.currency.format` | string | Currency format string (e.g. `"${{amount}}"`) |
| `window.Theme.mobileMenu` | object | Mobile menu instance |

---

## JS File Structure

```
scripts/
├── api/
│   └── shopify-cart.js          # Cart add/update API
├── components/
│   ├── announcement-bar.js      # Announcement bar scroll
│   ├── cart.js                  # Cart panel + templates
│   ├── header-scroll.js         # Header scroll detection
│   ├── mobile-menu.js           # Mobile navigation
│   ├── product-form.js          # Product form web component
│   ├── product-purchase-type.js # Subscription/one-time toggle
│   ├── product-recommendations.js # Related products
│   ├── product-variant-selector.js # Variant option selector
│   ├── search.js                # Predictive search
│   ├── signup-modal.js          # Email signup modal
│   ├── sticky-add-to-cart.js    # Sticky ATC bar
│   ├── text-modifiers.js        # Text animation effects
│   └── video.js                 # Video overlay component
├── packages/
│   ├── AOS.js                   # Animate on scroll
│   ├── tarot.esm.js             # Tarot carousel core
│   ├── ripple.js                # Ripple effect plugin
│   ├── spotlight.js             # Spotlight effect plugin
│   └── sliding-window.js        # Sliding window effect plugin
├── templates/                   # JS template literals
└── scripts.js                   # Main entry point
```

---

## Source Files

- `scripts/scripts.js` — Main entry point, all imports
- `scripts/api/shopify-cart.js` — Cart API (addVariantToCart, addFormToCart)
- `scripts/components/*.js` — All component implementations
