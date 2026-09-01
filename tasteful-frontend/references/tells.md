# Tells: the rut, mapped by era

You did not learn one style. You learned five strata of the web, laid down over twenty years,
and you reproduce whichever one the prompt's keywords fall nearest to. Knowing the strata by
name is how you notice what you are about to regurgitate. Each era also left something worth
keeping; that is listed too, because the goal is judgment, not a longer ban list.

## The strata

**2008 to 2012, glossy and Bootstrap.** Gradient buttons with a reflection, drop shadows on
everything, ribbon badges, a full-width slider hero with arrows and dots, a 960 grid, three
columns of circle icons, "Sign up, it's free!", stock handshake photos, Lobster and Museo,
striped backgrounds, Helvetica by omission. *Kept:* nothing structural. The one honest lesson is
that a page had a clear job and a single obvious action.

**2013 to 2016, flat and the one-page startup.** Full-viewport photo hero with a dark overlay
and centered white text, ghost buttons, parallax backgrounds, long-scroll one-pagers, hamburger
for everything, Montserrat, Raleway, Open Sans, Lato, flat icons in colored circles, testimonial
carousels, "as seen on" logo strips, big centered H1 plus subhead plus two buttons. *Kept:* the
discipline of one type of content per section. Photography at scale can still lead a page when
the photo is real and specific.

**2017 to 2020, SaaS gradient and Dribbble.** Purple-to-blue gradients, gradient text,
blob-people and isometric illustrations, Poppins, Inter, Nunito, 16-pixel radius on every card,
icon-in-a-colored-square feature grid, a floating screenshot in a device frame, side-stripe
cards, glowing shadows on dark, neon dark mode, pricing tables with a "most popular" badge,
"trusted by 10,000 teams". *Kept:* real product screenshots (without redrawn chrome), and the
idea that a marketing page should show the product working.

**2021 to 2023, aurora and glass.** Aurora mesh blobs behind the hero, glassmorphism panels,
floating 3D orbs, neumorphism, grain over everything, gradient borders, spinning "coming soon"
stickers, marquees everywhere, cursor followers, everything-fades-up-on-scroll, Lottie
checkmarks, Space Grotesk and Syne, huge tracking-tight sans, the Framer template look, bento
grids copied from Apple. *Kept:* bento when the content genuinely has size variance; blur as a
specific depth effect over real content; grain at very low opacity when the material is paper
or film; marquee as a deliberate kinetic device, paused on hover, once per page.

**2024 to 2026, editorial revival, or the anti-slop that became slop.** Cream paper, a
high-contrast serif display, a terracotta or signal-red accent. Near-black with one acid-green
or vermilion accent and glowing edges. Broadsheet hairlines, zero radius, dense columns, mono
eyebrows like `01 / FEATURES`, numbered sections, a label-left heading-right two-column head.
An italic emphasis word inside a roman headline. Fraunces, Instrument Serif, Newsreader, Geist,
Geist Mono, JetBrains Mono. A floating pill nav with backdrop blur. A ⌘K badge. A terminal
aesthetic with a blinking caret in the corner. Sticky-scroll feature stacks. Monochrome logo
walls. Koan and refusal headlines ("Made, not generated."). A stat-led hero. *Kept:* almost all of it is good
craft when the brief asks for that world. The failure is that it appears regardless of subject.

Slop is not low quality. Generator-default pages are often clean, fast, and accessible. They are
slop because they are interchangeable: a fintech page that could be a CRM page with zero
changes. That is why the fix is never "the other default". It is a decision derived from the
subject.

## Tells and fixes

Severity: **critical** ships as generated; **major** reads as generated; **minor** reads as
unproofed. Two majors in one view equal a critical.

### Structure

- **The template silhouette** (critical). Hero, three cards, band, CTA, four-column footer.
  *Fix:* choose a page shape from the content (document, poster, product tour, split, index,
  catalogue, diagram, letter, specimen, stacked scroll) and vary rhythm down the page.
- **Full-viewport centered hero** with one sentence and one button (critical). *Fix:* let the
  hero be the height of its content, give it a primary axis, put the mechanism in it.
- **Three equal columns of icon, heading, two lines** (critical). *Fix:* vary widths and
  heights, pull icons inline, drop a card for negative space, or use typographic rhythm with
  no cards at all.
- **Card in card** (critical). *Fix:* one containment layer, usually the outer one goes.
- **Eyebrow on every section**, especially `01 · THE TOUR` style (major). *Fix:* eyebrows off by
  default. Number only a real sequence. When one is used, stack it above the heading in the
  same column. A label in a column beside the heading is banned outright.
- **Hero-metric template**: giant number, small label, supporting stats, accent (major).
  *Fix:* only when the number is real and is the argument; pair it with a worded headline.
- **The AI nav**: wordmark left, four or five links, button right, full width, sticky, white,
  hairline below (major). *Fix:* give the nav the page's voice. A masthead, an edge-aligned
  pair, a side rail, a bar that morphs on scroll, a single inline line. Two destinations may
  earn a minimal bar.
- **The AI footer**: four link columns, social row, tiny copyright (major). *Fix:* close the
  page. A statement, a single dense colophon line, a masthead repeat, a letter sign-off, a
  newsletter-first close. Index columns only on a real hub with a real sitemap.
- **Every section padded identically**, separated only by equal whitespace (major).
  *Fix:* tighten one, expand another, shift a background, cross the grid once.
- **Sticky-scroll feature stack** used because it looked premium elsewhere (minor).
  *Fix:* only when the content is genuinely a sequence of views of one thing.

### Surface

- **Purple-to-blue, cyan-to-magenta, orange-to-pink gradients** anywhere, especially as text
  fill (critical). *Fix:* one anchor hue; solid ink for headlines; emphasis by weight or size.
- **Aurora mesh, floating orbs, blurred colored circles** behind a hero (critical).
  *Fix:* cut them, or replace with one material the world actually has (paper, film grain,
  a real photograph, a drawn texture, a shader that responds to input).
- **Glass panels as decoration** (major). *Fix:* glass only as a specific depth effect over
  real content that needs to show through.
- **Side-stripe card**, a colored border-left thicker than 1 pixel (major). *Fix:* hairline all
  around, no border, or a small accent mark beside the heading.
- **Hard offset block shadow** outside a world that is actually neobrutalist (major). *Fix:*
  a shadow with offset and soft blur, or lightness-based elevation on dark.
- **Colored glow shadow** on a dark card (major). *Fix:* elevation by lightness, about 3
  percent per level; shadows on dark stay tight and dark.
- **Pure #000 or #fff** as a base surface (minor, and allowed for a deliberately monochrome
  world). *Fix:* tint toward the anchor hue.
- **Zero-chroma grey text on a colored surface** (major). *Fix:* derive secondary text from the
  surface hue or the foreground.
- **Grain, noise, or texture over everything** (minor). *Fix:* one material, bounded, under
  0.1 opacity, only when the world is paper or film.
- **Geometric mask standing in for an organic edge** around a photographic subject (major).
  *Fix:* a real alpha matte or a cut-out asset, or omit the effect.
- **16-pixel radius on every element** (minor). *Fix:* a radius scale with a reason: sharp for
  print worlds, tight for instrument worlds, round only where the object is round.

### Type

- **Inter, Roboto, Open Sans, Poppins, Lato, Montserrat, or the system stack as the display
  voice** (critical). *Fix:* a display face with a point of view, chosen from the subject's
  world. See `type-and-color.md`.
- **The 2026 reflex pairing**: Fraunces, Instrument Serif, or Newsreader over Geist, with Geist
  Mono or JetBrains Mono as the label face (major when the brief left type free). *Fix:* look
  past the first three faces you thought of.
- **Italic emphasis word inside a roman heading**, or an all-italic display (major). *Fix:*
  roman headings; emphasis by weight, color, or a drawn underline. Italic lives in body copy.
- **Gradient text** (critical). *Fix:* solid ink.
- **Monospace as a costume for "technical"** rather than for code, data, or measurement
  (major). *Fix:* mono for what is typed or measured; the display voice is something else.
- **Uppercase tracked labels everywhere** (minor). *Fix:* one label register, used where a
  label is information.
- **Display size over about 6 rem**, or a 100-character headline at display size (major).
  *Fix:* size by copy length; under 50 characters gets the full display, over 90 gets
  rewritten.
- **All-caps display with line-height under 1.0** (minor). *Fix:* floor at 1.0, prefer 1.02
  to 1.08.
- **Straight quotes, `--` for dashes, `...` for ellipsis** (minor). *Fix:* curly quotes, real
  em and en dashes, a real ellipsis.

### Color

- **Accent as background fill across whole sections** in a world that chose Restrained (major).
  *Fix:* accent is a highlighter in Restrained. Committed and Drenched strategies own color at
  page scale on purpose, as fields, not as scattered accents.
- **Color picked by category** (blue for fintech, green for health, black for luxury) (major).
  *Fix:* hue from the subject's actual materials and the use scene.
- **Light or dark picked by category** (dark for "AI tool", light for "friendly") (major).
  *Fix:* one sentence of physical scene decides it.
- **Dark mode as a mechanical invert** (major). *Fix:* compose it: paper at 12 to 18 percent
  lightness, ink at 92 to 96, accent with slightly less chroma and more lightness, elevation by
  lightness, body weight down one step.
- **Red and green as the only state signal** (major). *Fix:* add an icon, text, or pattern.

### Motion

See `motion.md` for the full list. The headline tells:

- `transition: all` (major). Universal `hover:scale-105` (major). Bounce or elastic easing on UI
  state (major). Everything fades up on scroll (major). Cursor followers (critical). Animated
  focus rings (major). Auto-rotating carousels with no pause (critical). Celebratory toast for a
  visible success (major). A confirm dialog for a reversible action (major). A blob that drifts
  because something had to move (major). A blinking caret floating in a hero (major).

### Content

- **Invented metrics, testimonials, logos, or prices** (critical). *Fix:* real numbers from the
  user, a labeled placeholder, or a section shape that does not need proof.
- **"Jane Doe", "John Smith", "Acme", "Nexus", "Lorem ipsum"** (minor). *Fix:* plausible,
  domain-specific names.
- **Distribution-default lines**: "built for the modern team", "unleash", "where X meets Y",
  "seamless", "supercharge", "reimagine", "in today's digital landscape", "next-generation"
  (major). *Fix:* a specific noun, verb, place, or number from the subject.
- **Riddle headlines**: the two-word koan, the pun, the refusal ("Not a platform."), the
  "X, but for Y" analogy, the poetic fragment that needs the subhead to decode it (major).
  *Fix:* name the thing. What it is, who it is for, what it does, in plain words.
- **Hedged copy**: "may help", "best-in-class", "designed to" (minor). *Fix:* say what it does.
- **"Oops!", "Something went wrong"** (major). *Fix:* name what broke and what to do.

### Assets and chrome

- **Redrawn browser bar, phone frame, code-window dots, or IDE chrome** around a screenshot
  (critical). *Fix:* the screenshot in a figure with at most a hairline, or no frame.
- **Emoji as icons** (critical). **Mixed icon libraries** (major). *Fix:* one library or
  authored SVG in one stroke weight; or no icons, typography leads.
- **AI-smooth illustration**: symmetric, plastic, no joint articulation; or corporate doodle
  people (critical). *Fix:* typography only, pure CSS art, hand-built SVG, or a generated still
  with real art direction and post-processing.
- **Stock photo of diverse people at laptops** (critical). *Fix:* the subject's own object,
  one decisive image, or none.
- **Lottie for a checkmark, spinner, or logo spin** (major). *Fix:* CSS or SVG.
- **Three.js for a still object nobody can touch** (major). *Fix:* a photograph or SVG.
- **A hero video with sound, no poster, or lazy-loaded** (major). *Fix:* `autoplay muted loop
  playsinline`, a poster, `fetchpriority="high"`; lazy only below the fold.

### Code

- **A hex, OKLCH, or font-family outside the token block** (major). *Fix:* lift it into the
  tokens, then reference.
- **`width: 100vw`** (minor). **`z-index: 9999`** (minor). **`overflow-x: hidden` on the root**
  (minor; use `clip`). **`outline: none` without a replacement** (critical).
- **Selector specificity fights** between section rules and element rules, especially for
  spacing (minor). *Fix:* one layer of layout rules, spacing through `gap` and tokens.
