---
name: magic-spells-design-system
description: >
  Triggers on any design work in the "Magic Spells Starter Template" Figma file:
  building or editing section boards, page templates, components, variables, or
  text styles through the figma-mcp-bridge; translating Luna (or other Magic
  Spells theme) sections into Figma; or auditing existing boards. Covers the
  mode-driven architecture, sizing rules, token usage, verification discipline,
  and Luna breakpoint mapping.
version: 1.0.0
---

# Magic Spells Design System — Figma Skill

How sections are designed in the "Magic Spells Starter Template" Figma file.
Assumes figma-mcp-bridge **v0.4.0+** (mode setters, node-level `boundVariables`
readback, working text styles, file-first exports). On an older bridge, check
`figma_server_info` and expect the workarounds this skill deliberately omits.

---

## 1. The architecture — one mode-driven component per section

Every theme section is **ONE component**. Breakpoint behavior comes from
**Figma variable modes**, never from a `Device=` variant axis. A variable mode
cannot drive a variant property, so a device axis can never respond to the
Appearance dropdown — and it multiplies combinatorially against the color
schemes.

Three variable collections carry the modes:

| Collection | ID | Modes |
|---|---|---|
| Spacing | `VariableCollectionId:6:2488` | mobile `6:0` / tablet `2145:0` / desktop `11:2` |
| Font Sizes | `VariableCollectionId:2383:6437` | mobile `2383:0` / tablet `2383:4` / desktop `2383:5` |
| Color Schemes | `VariableCollectionId:2065:905` | 4 schemes (not breakpoints) |

Each section board holds three preview frames (375 / 768 / 1440), each pinned
to an explicit variable mode via `figma_set_variable_mode`, each holding one
instance of the section master. The user sets Appearance per frame and the
section re-renders. That is the whole point of the file.

## 2. The rules

1. **Sizing belongs to the container, never the component.** Use auto-layout
   `fill`, `hug`, and `layoutGrow: 1` (Figma's `flex-1`). A card, column, or
   slide does not know its own width — the grid or carousel it sits in decides.
2. **Never create a variable to solve a width, height, or aspect ratio.**
   Needing a new token for a size is the tell that the layout is wrong. The
   existing spacing and type scales are complete. (~30 such tokens were created
   once; the user rejected and deleted all of them.)
3. **Masters carry no fixed sizes.** A master with a baked-in image size
   (Product/Collection/Article Card's old mistake) can never adapt, no matter
   how the container is built.
4. **Never pin a variable mode on a master.** Pins belong on preview and page
   frames only. On a master every instance inherits the pin; because pins are
   per-collection you get half-correct output (type scales, spacing frozen)
   that hides the fault. Check with `explicitVariableModes` in a node read;
   clear with `figma_set_variable_mode` + `clear: true`.
5. **Bind spacing and font sizes to the mode collections** — house pattern:
   `layout/gutter` for section left/right padding, `layout/section-v-padding`
   for top/bottom, `layout/gap-*` and `spacing/*` for internal gaps. Use
   spacing tokens whenever a value matches one; literal only when no token fits.
6. **Bind every surface color to the Color Schemes collection** —
   `Base/background`, `Base/text`, `Base/border` — so the Appearance settings
   can swap schemes. Never hardcode a fill, stroke, or text color.
7. **Don't repeat the section title inside the section panel.** The board
   header already names it.
8. **Preview frames are always ordered Mobile → Tablet → Desktop**, left to
   right. Name boards after the theme section file (`featured-collection`,
   not "Featured Products") so theme and design stay traceable.

## 3. Text styles

Text uses shared styles, with sizes bound to the Font Sizes collection so they
stay mode-driven:

- **Heading 1, Heading 2** (bind `fontSize` to `heading/h1`, `heading/h2`)
- **Body Lg, Body Md, Body Sm** — **Body Md is the default global body text**

Create with `figma_create_text_style`, bind via `figma_set_variable` with
`styleId`, apply with `figma_apply_style`. Verify the bind by reading the
style's `boundVariables` in the response — a text style with a literal size
actively breaks mode-driven type wherever it's applied.

## 4. Building a section — the method

1. **Read the theme source first.** Every good layout decision comes from the
   Liquid; every bad one from assuming. Check the section file's Tailwind
   classes AND the CSS custom properties behind them (`--section-padding` is
   40/45/50/60/80 across five breakpoints, and `py-section` applies ×1.1 to
   the bottom).
2. **Map Tailwind prefixes to real pixels.** Luna: `sm` 576, `md` 768,
   `mde` 896, `lg` 1024, `xl` 1280. This matters enormously: `lg:grid-cols-3`
   is still **one column at 768**. That is correct behavior, not a bug —
   an agent once nearly "fixed" it.
3. **Build the master** with container-driven sizing (rules 1–3), mode-bound
   spacing/type (rule 5), scheme-bound colors (rule 6). Responsive grids
   reflow with auto-layout `layoutWrap: 'WRAP'`; verify wrap engaged by
   reading `layoutWrap` and `counterAxisSpacing` back.
4. **Build the three preview frames**, pin each frame's Spacing + Font Sizes
   modes with `figma_set_variable_mode`, drop in an instance, size it with
   `figma_set_layout_align: STRETCH` (preserves size binds; `resize` may
   destroy them — the bridge re-applies and warns, but STRETCH is the right
   tool).
5. **Reproduce what the theme actually does, including when it looks wrong.**
   Luna's collection page really is cramped at exactly 768 (`hidden md:block`
   sidebar squeezing a 3-col grid). Faithful reproduction surfaces real theme
   bugs; quietly "improving" it hides them.

## 5. Verification — before reporting done

1. **Read the bindings.** `figma_get_nodes` (full) returns `boundVariables`
   and `explicitVariableModes` — confirm every intended bind and pin directly.
2. **Export the render and look at it.** `figma_export_node` writes a PNG to
   disk — Read the file and actually look. Property readback cannot see
   composition: real sessions passed every numeric check while renders showed
   a portrait video well where 16:9 belonged, a heading breaking mid-word,
   and a button hugging at 140px instead of spanning its form. Export all
   three previews; compare against the theme at those widths.
3. **Report honestly.** If a tool errors or a readback disagrees with the
   response, report the failure — never paper over a gap by hardcoding a value.

## 6. What Figma genuinely cannot express

State these plainly instead of working around them:

- **No aspect-ratio binding.**
- **No responsive reordering** — the theme's `md:order-*` cannot be modeled.
- **No x/y variable binding** — a CSS bento grid with `col-span`/`row-span`
  cannot be one mode-driven component. Luna's `collection-list-bento` is
  deliberately unbuilt for this reason.

## 7. Bridge etiquette (multi-agent sessions)

- One WebSocket, one open document: at most ~2 write-heavy agents; retry
  transient `Unable to establish connection` errors.
- **Never call `figma_set_current_page` or `figma_set_selection` from a
  sub-agent** — it yanks the shared view out from under other clients.
- Instance sublayers cannot take size binds or resizes — the bridge now
  errors (`INSTANCE_SUBLAYER_RESTRICTED`) instead of pretending; fix the
  master instead.

## 8. Reference — key IDs

**Pages:** Sections `22:4` · Components `2156:637` · Templates `2065:846` ·
Home `2080:710` · Collection `2080:748` · Product `2080:749` · FAQ `2080:751` ·
Contact `2080:752`

**Containers:** Content Sections H-Stack `2668:3326` · Global Sections
H-Stack `2695:4215`

**Reusable components:** `Button` set `32:3` (solid primary `24:2475`, outline
primary `32:8`, solid white `2270:2233`) · `Text Field` set `2395:2396` ·
`Checkbox` set `2395:2013` · `Carousel Button` set `2394:1836` · `Page Dots`
`2394:1814` · `_Design Board Header` `2360:279` · `Image Placeholder`
`2657:2446` (ships with cornerRadius 16 — override to 0 for full-bleed)

**Spacing tokens:** `layout/gutter` `11:2523` (16/24/48) ·
`layout/section-v-padding` `2020:902` (40/50/60) · `layout/gap-md` `21:3` ·
`layout/gap-lg` `21:2` · `spacing/xs|sm|md|lg|xl` `11:2526` `11:2525`
`11:2522` `11:2520` `11:2527`

**Font Size tokens:** `heading/h1` `2383:7128` (40/49/67) · `heading/h2`
`2383:7136` (33/39/51) · `heading/h3` `2383:7140` (28/31/38) · `heading/h4`
`2383:7134` (23/25/28) · `heading/overline` `2383:7143` (14 flat) ·
`body/body` `2383:7132` (16 flat) · `body/body-lg` `2654:2368` (18 flat)

**Color Scheme tokens:** `Base/background` `2065:906` · `Base/text` `2065:907`
· `Base/border` `2366:3631`

**Corner Radius:** `theme rounded` `2653:2291` (12) · `button` `2020:899` (8)
· `input field` `2020:901` (8)

## 9. Known defects in the file (as of Aug 2026)

Check whether these are fixed before designing around them:

- **Card masters bake image sizes** — `Product Card` `2661:2719`,
  `Collection Card` `2659:2631` (whose `Text=On Image Bottom Left` variant —
  the theme's default `card_style` — is missing its text layer entirely),
  `Article Card` `2662:3136`. Sizing across the 22 boards needs redoing with
  proper fill/hug/`layoutGrow` — frozen literals remain where rejected width
  tokens were unbound.
- **`Image Placeholder` `2657:2446`** ellipses have MIN/MIN constraints — fix
  is SCALE/CENTER on the master, not per-instance wrappers.
- **`Section Header` `2671:3967`** — no boolean props to hide unused children,
  stray −0.017° rotation; usually faster to build a minimal overline + h2.
- **`Inputs/*` tokens are pure white in schemes 1 and 3**; **`Option button/*`
  tokens are pure white in all four schemes** — the latter blocks
  `product-main` (invisible variant pickers).
- **`Header` is still two device-split component sets** (`2668:3447` desktop,
  `2668:3272` mobile) — the one non-mode-driven seam in every page template.
