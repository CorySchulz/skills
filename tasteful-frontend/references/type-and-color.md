# Type and color

Type carries the personality; color carries the temperature. Both are chosen from the subject's
world, never from the category and never from the first face or hue that comes to mind.

## Choosing faces

Two families is canonical: a display face with a point of view and a body face that disappears
into reading. A third is allowed as an outlier for one register (wordmark, hero figure, pull
quote, code) and appears in at most two places on the page. Four families is slop. A
single-family page is allowed only when the single face is the design: a genuinely monospace
world, a poster in one display face, a product UI where one well-tuned sans does every job.

Operate and Read surfaces are well served by workhorse UI faces and even system stacks; a
tool should disappear into its task. Persuade and Experience surfaces want faces with a point
of view. Choose them like objects from the subject's world: what would this thing look like as
a physical object; what did its world print, engrave, stamp, or letter before the web?

**The spent list.** These are what you reach for by reflex. Naming one requires a reason no
other face could satisfy, and a subject association is never that reason (books wanting a
serif, tech wanting a mono, a bakery wanting hand lettering are the associations this list
exists to break):

Inter, Inter Tight, Roboto, Open Sans, Lato, Poppins, Montserrat, Raleway, Nunito, Work Sans,
DM Sans, DM Serif, Outfit, Plus Jakarta Sans, Instrument Sans, Geist, Geist Mono, Manrope,
Figtree, Satoshi, Syne, Space Grotesk, Space Mono, IBM Plex (all), JetBrains Mono, Fraunces,
Playfair Display, Instrument Serif, Newsreader, Cormorant, Lora, Crimson, Merriweather, Bebas
Neue, Oswald, Anton, Abril Fatface, Lobster, and the platform sans used as a display face.

**Range, not a menu.** Faces below are examples of how far the free catalogs reach, grouped by
voice. They are not a replacement allowlist; the moment a list becomes your default it joins
the spent list. All are on Google Fonts or Fontshare unless marked.

- *Expressive serif:* Gloock, Young Serif, Libre Caslon Display, Bodoni Moda, Castoro, Rufina,
  Besley, Vollkorn, Gambetta (Fontshare), Zodiak (Fontshare), Boska (Fontshare), Erode
  (Fontshare), Sentient (Fontshare), Melodrama (Fontshare).
- *Reading serif:* Literata, Source Serif 4, Spectral, Piazzolla, Faustina, Alegreya, EB
  Garamond, Gelasio, Cardo.
- *Grotesk and neo-grotesk:* Familjen Grotesk, Schibsted Grotesk, Hanken Grotesk, Host
  Grotesk, Onest, Golos Text, Public Sans, Archivo, Reddit Sans, Wix Madefor, Cabinet Grotesk
  (Fontshare), General Sans (Fontshare), Switzer (Fontshare), Author (Fontshare).
- *Geometric and humanist sans:* Sora, Albert Sans, Gabarito, Afacad, Be Vietnam Pro, Red Hat
  Display, Epilogue, Kumbh Sans, Lexend, Atkinson Hyperlegible, Supreme (Fontshare), Ranade
  (Fontshare).
- *Condensed, wide, and display sans:* Big Shoulders, Archivo Narrow and Black, Unbounded,
  Darker Grotesque, Funnel Display, Bricolage Grotesque, Anybody (variable width), Mona Sans
  and Hubot Sans (GitHub, variable width), Clash Display and Clash Grotesk (Fontshare), Tanker
  (Fontshare), Chillax (Fontshare), Panchang (Fontshare), Khand (Fontshare), Array (Fontshare).
- *Mono:* Fragment Mono, Martian Mono, Azeret Mono, Sometype Mono, Chivo Mono, Red Hat Mono,
  Spline Sans Mono, Reddit Mono, Commit Mono (self-hosted), Berkeley Mono (paid).

Variable axes are an expressive tool you under-use: width (Anybody, Mona Sans, Archivo), optical
size (Literata, Source Serif, Piazzolla, Bodoni Moda), and weight animated on hover or scroll
when the world earns it. Load only the axes and weights you use.

Paid foundry faces (Klim, Commercial, Pangram Pangram, Colophon, Lineto, Grilli, Dinamo) may be
named only when the user confirms a license. A paid face named without one falls back to the
system stack and reads broken.

## Setting type

- Scale by ratio, not increments: 1.2 for dense product UI, 1.25 to 1.333 for marketing, 1.5 or
  above for poster worlds. Use no more than five sizes on a page; get further hierarchy from
  weight, color, and space.
- Display: `clamp()` for marketing, fixed rem for product UI. Cap display around 5.5 to 6 rem;
  a single short word may go larger. Size the hero headline by length: under 50 characters
  gets the full display, 51 to 90 steps down, over 90 gets rewritten.
- Tracking: tight on display (-0.02 to -0.04 em depending on the face, floor -0.04), loose on
  small uppercase labels (0.06 to 0.14 em), never above 0.05 em on body.
- Line height: 1.0 to 1.15 on display (floor 1.0 for uppercase), 1.5 to 1.65 on body, tuned
  to the face and measure. Light text on dark gets a touch more leading, tracking, and weight.
- Measure: 45 to 75 characters for prose, `max-width: 65ch` as the default. Tables and dense
  UI may run wider.
- Weights: contrast the display and body by at least 300 units, or make the contrast come from
  the faces themselves. Never synthesize bold or italic; load the weight.
- Body floor 16 px on the web; nothing under 12 px. Headings are roman. Emphasis in interface
  text is bold, not italic; underlines are for links only.
- Features: `font-variant-numeric: tabular-nums` on any column of numbers; oldstyle figures in
  running prose where the face has them; real punctuation (curly quotes, em and en dashes, a
  real ellipsis, non-breaking spaces before units); `text-wrap: balance` on headings and
  `text-wrap: pretty` on prose; `hanging-punctuation` where supported.
- Loading: `font-display: swap`, metric-matched fallbacks (`size-adjust`, `ascent-override`,
  `descent-override`) to kill layout shift, subset when self-hosting.
- Paragraph rhythm: space between paragraphs or a first-line indent, not both.

## Color strategy first, colors second

Name the strategy before choosing values:

- **Restrained**: tinted neutrals plus one accent. The default when the visitor came to
  operate or read. The accent is a highlighter: active states, focus, links, the primary
  action, one small mark. It covers a few percent of any viewport.
- **Committed**: one saturated color owns 30 to 60 percent of the surface as fields that own
  whole regions, not accents scattered over a neutral ground.
- **Full palette**: three or four named roles with real jobs, each consistent across the page.
- **Drenched**: the surface is the color; ink and one contrast color do the rest.

Persuade and Experience may take any of the four. Operate and Read default to Restrained and
may earn Committed for one surface (a welcome screen, a report where one category leads).

Light or dark is never a default. Write one sentence of physical scene, who uses this, where,
under what ambient light, and let it decide. Never switch the hue between modes; only lightness
and chroma move.

## Building a palette

Work in OKLCH so lightness and chroma move predictably. Hue comes from the subject's
materials, not the category.

- **Paper** (base surface): light `oklch(95–98% 0.005–0.02 H)`, dark `oklch(12–18% 0.008–0.02 H)`.
- **Ink** (primary text): light mode `oklch(15–22% 0.005–0.02 H)`, dark mode `oklch(92–96% 0.005–0.01 H)`.
- **Neutrals**: five to nine steps between paper and ink, each carrying a trace of the anchor
  hue (0.005 to 0.02 chroma). Zero-chroma grey is allowed only in a deliberately monochrome
  instrument world.
- **Accent**: meaningful chroma (0.12 to 0.22), and an `accent-ink` role guaranteed to read on
  it. Never hard-code white on the accent.
- **Focus**: a color with at least 3:1 against both the element and the page.
- **Semantic**: success, warning, error, info, each paired with an icon or text; never color
  alone.
- **Dark mode**: paper 12 to 18 percent, ink 92 to 96, accent minus 0.02 to 0.04 chroma and
  plus 5 to 10 percent lightness, elevation by lightness (about 3 percent per level), body
  weight down one step.

On colored surfaces, secondary text is derived from the surface hue or the foreground, never
generic grey. Reduce chroma near white and black instead of keeping it uniform for the math.
Prefer explicit colors to stacks of translucent overlays when contrast would become
context-dependent. Two-stop gradients only, and only as a material the world has (a sky, a
lacquer, a photograph's light), never as decoration.

Name every token by role, not value: `--color-paper`, `--color-ink`, `--color-accent`,
`--color-accent-ink`, `--color-rule`, `--color-focus`. In Tailwind v4, define them in `@theme`
and reference by name; no arbitrary values across components.

## Contrast

Verify computed pairs, not intentions: body text 4.5:1 (APCA Lc 60), large text, icons, and
focus rings 3:1 (Lc 45). The pairs that fail most often: text inheriting ink inside a section
that flipped to a dark background (any rule that sets a dark background also sets the text
color); muted text on a tinted secondary surface; white on an accent that is too light; a
focus ring that matches the button it sits on. Check both themes and every state, and simulate
common vision deficiencies when a browser is available.
