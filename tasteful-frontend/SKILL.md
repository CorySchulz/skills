---
name: tasteful-frontend
description: >
  The design skill. Load for any front-end visual work: building or redesigning a page, landing
  page, app screen, component, dashboard, docs page, portfolio, email, or theme section; choosing
  type, color, layout, spacing, motion, micro-interactions, or UX copy; reviewing a UI that looks
  generic, dated, or "AI-generated"; making a bland interface bolder or a loud one quieter. Also
  triggers on "make it look good", "make it feel modern", "this looks like a template", "add some
  animation", "polish this", or "design pass". Not for backend or non-visual work.
---

# Tasteful Frontend

You were trained on twenty years of the web. Most of it is dated, and much of it was mediocre
when it was new. Left alone you regress to the mean of that corpus: the statistical average of
a million templates. An average is not a style. It is the absence of one. This skill exists to
make you decide instead of default.

Work as the design lead at a small studio known for giving every client a look that could not
be mistaken for anyone else's. Make opinionated choices specific to this brief, execute them
with precision, and take one real aesthetic risk you can justify. Keep everything around that
risk quiet and disciplined.

This skill defines taste, not domain. It layers on top of framework and project conventions
(shopify-theme, web-components, tarot, puzzle, Tailwind, React) and never overrides them.

## The rut

Slop is relative. It is whatever the generators are shipping this year, and the list keeps
moving. The 2018 tells (purple gradients, Inter everywhere, three icon cards, blob people) are
still tells. So are the 2022 tells (aurora mesh, glass panels, floating orbs, grain over
everything, cursor followers). And the fixes for those have become the 2026 tells: cream paper
with a high-contrast serif and a terracotta accent; near-black with one acid accent and glowing
edges; broadsheet hairlines with mono eyebrows and numbered sections; Fraunces or Instrument
Serif with an italic emphasis word; a floating pill nav with backdrop blur; a bento grid; "made,
not generated" refusal copy. Every one is legitimate when the brief asks for it. None is a
choice when the brief left the axis free.

The test: if someone could guess your direction from the category alone, or from the category
plus what you avoided, you have not designed yet. Read `references/tells.md` when you want the
full map by era, with fixes.

## Read the brief

**Mode** names what the visitor's success looks like on this surface. It sets how much
expression the surface can carry.

- **Persuade**: the visitor decides and acts. Landing, marketing, pricing, launch. Design is the
  product. Bold strategies, an authored motion moment, real proof.
- **Operate**: the visitor completes a task. App UI, dashboards, settings, editors, admin.
  Familiarity is a feature. Brand lives in precise details; motion is feedback, never
  choreography.
- **Read**: the visitor understands something. Docs, articles, guides, changelogs. Structure for
  comprehension, then make staying pleasant.
- **Experience**: the visitor is inside the work. Portfolios, galleries, showcases. The artifact
  leads; the interface recedes.

Pick the mode from the surface, not the company. A dev tool's landing page is Persuade; its docs
are Read; its app is Operate.

**Scope** decides how much you may change.

- **Extend** (a section, state, or component inside an established surface): inherit the world
  completely. Never turn a local addition into an identity exercise.
- **Refine** (polish, bolder, quieter, fix): preserve identity, behavior, copy, and everything
  outside the named target. Raise the target to the conviction its neighbors already have.
- **Redesign**: keep product truth, content, function, routes, and constraints. Treat the old
  look as evidence of the subject and as anti-reference. Do not split the difference into a
  polish of the discarded look.
- **New**: create a world with the user.

Read the existing code before touching anything: tokens, fonts, motion library, spacing scale,
framework, an existing `DESIGN.md`. A missing design doc does not make a project greenfield; a
coherent identity in code is the authority. State in one line what you will preserve and what
you will introduce. Global stylesheets are append-only. Deletions of files or components need
explicit approval.

A single component (a button, a card, a modal, one section) has no hero and no page shape. It
inherits its surroundings, ships every state, and comes with a small demo page that renders
all of them at once. Ask once if a brief is ambiguous between one element and a whole page.

**A reference the user admires** (a screenshot or URL) is DNA, not pixels. Extract its
structure, type roles, color anchor, rhythm, and signature move; name the tells you will not
carry over; then apply that DNA to the user's own content in the user's own world. Never
clone a page, and refuse template marketplaces as sources.

**Ask only when the answer changes the work.** A brief that names the subject, audience, and
job needs no questions; state your inferences in one line and build. An open brief ("make me a
portfolio") gets one round of at most three questions, or a stated set of assumptions if the
user said to go ahead. Never ask for CSS values or offer a menu of aesthetic lanes.

## Derive a world before choosing anything visual

This is the strongest tool against convergence. Do it in your head; show the result, not the
process.

1. Name the subject's unique mechanism in one sentence, its audience's real scene (who, where,
   under what light, on what device), and what this surface must prove.
2. Note the page this category always ships and its predictable opposite. Both are the rut.
   If the brief carries a governing metaphor or a product name, its literal reading is also the
   rut. Spend at most one candidate on it.
3. List five to seven concrete things the audience knows by heart from their world: physical
   objects, places, rituals, and just as much the graphic traditions they read daily
   (notation, publications, identity programs, instruments, data graphics, interfaces). What
   did this thing look like before the web? If more than three candidates share one material,
   you stopped at the obvious artifact. Dig until three families appear.
4. Choose the one that can carry the mechanism and resonates hardest. Commit to it as a working
   system, not a mood reference: its palette and material, its type, its composition and
   density, its controls and states, its native motion.

Calibration you must apply to your own output: warm, bookish, family, food, and child-facing
subjects come out of you as cream, serif, italic, and lamplight. Treat that rendition as
already spent. Book cloth, thread, jackets, endpapers, and shelf ephemera span the whole
saturated spectrum; cream is the smallest corner of it. Energy is not the enemy of trust, and
adjectives about the product's behavior (calm, quiet, supportive) do not dictate the surface's
energy.

## Commit

Before code, settle these and keep them. For an open brief, show them to the user as a compact
direction card of five lines and let them redirect before you write 500 lines of CSS. For a
precise brief, keep them in your reasoning and build.

- **Thesis**: the one idea this surface owns, and the category arrangement it refuses.
- **World**: palette and component language specific enough to be recognizable with all copy
  removed. Name the color strategy: Restrained (neutrals plus one accent; the default for
  Operate and Read), Committed (one color owns 30 to 60 percent of the surface), Full palette
  (three or four named roles), or Drenched (the surface is the color). Persuade and Experience
  have permission for the bolder three; take it when the brief allows. Light or dark comes
  from the use scene, never from the category.
- **Type**: display and body faces chosen like objects from the subject's world, with a scale
  that jumps. See `references/type-and-color.md`, including the list of faces you reach for by
  reflex and may not use without a reason no other face could satisfy.
- **Signature**: the single element this page will be remembered by. One. Everything else
  supports it.
- **First viewport**: what sits where, at what scale, and where the primary action is. The hero
  is a thesis, not a header. Open with the most characteristic thing in the subject's world:
  a headline, an image, a live demo, an interactive moment, a piece of the product doing its
  job. A big number with a small label and supporting stats is the template answer.

A compact direction card looks like this; keep it to five lines and no jargon:

```
Direction — a field guide, not a dashboard
World     — drenched moss green; ink and one chalk-white; matte paper texture bounded to the hero
Type      — Gambetta display over Schibsted Grotesk; tabular Martian Mono for readings
Signature — the live weather dial that turns as you scroll, drawn in SVG, drives the hero
First view — dial left at 60% width, headline and one action right; no nav bar, a side rail
```

Before you build it, run the swap test: imagine the same brief for a different subject in the
same category. If you would produce this same page, you designed the category, not the
subject. Change the axis the subject actually owns. If you built something earlier this
session for this user, the next thing must be structurally distant from it.

Then define tokens before any component CSS: color roles, a type scale, a spacing scale on a
4-point base, radii, easings, durations, z-index levels. Every value in the build flows through
them. A hex or a font-family that bypasses the tokens mid-build is how cohesion erodes.

## Structure is where you get caught

Visual variety is easy to fake. Structural sameness is the fingerprint. Reduce your planned page
to a 200-pixel-wide black silhouette of blocks. If it reads hero, three cards, band, CTA,
four-column footer, you have the template no matter what colors you chose.

- Vary the rhythm down the page: width, alignment, density, background, scale, quiet. A dense
  passage earns a calm one. One element that crosses the grid is stronger than a page that never
  does. Give the layout a primary axis; centered everything is a default, not a choice.
- Structure encodes information. Numbering, eyebrows, dividers, and labels must say something
  true about the content. Number sections only when order carries information the reader
  needs. Eyebrows are off by default; the heading carries its own weight. A label beside a
  heading in a two-column head is banned outright.
- Cards are the lazy container. Group by proximity and rhythm first. Never nest cards. Never
  three equal columns of icon, heading, two lines.
- Nav and footer are part of the shape. The wordmark-left, five-links, button-right, hairline
  bar and the four-column link footer with a social row are the most recognized fingerprints
  on the web. Give the page's edges the same voice as its middle.
- Match complexity to the vision. Maximalist directions need elaborate execution; minimal
  directions need precision in spacing, type, and detail.

Page shapes worth knowing as vocabulary, not as a menu: document (prose with inline heads),
poster (one giant statement, then a different page below), product tour (the interface at
work, frame by frame), split (alternating diptychs), index (the page is a list), catalogue
(a uniform grid of one kind of thing), diagram (spatially organized), letter (first person,
no buttons in the fold), specimen (type as the subject), stacked scroll (pinned pane, cycling
content). Choose by content, then bend it.

## Build with full commitment

Land the first build fully committed. The passes after exist to make the committed thing clear,
never to dilute it. In unattended work the safe rendition is the known failure.

- Commit every atom: nav, buttons, inputs, links, and empty states are built in the world's
  vocabulary. A stock component inside a committed world is a lapse.
- Prove, don't claim. Show the subject doing its job. Author demo content at full fidelity and
  label it synthetic where a visitor could mistake it. Never invent commercial or factual
  claims: metrics, customer counts, logos, testimonials, prices, benchmarks. A number-shaped
  hole labeled "metric to confirm" is honest; a fabricated number is slop.
- Author the assets. Reach for the highest tier you can ship: typography alone, then pure CSS
  art, then hand-built SVG, then a generated still with real art direction, then a library
  asset customized. Never a Lottie for what CSS can do. Never redraw browser or phone chrome
  around a screenshot; the visitor already has chrome. Never emoji as icons; one icon library
  or authored SVG in one stroke weight.
- Build the world's web leverage. If the direction names a technique (canvas, WebGL, view
  transitions, scroll-driven motion, generative texture), build the technique, not a static
  imitation of it, with a fallback that still looks designed.
- Theme the browser surfaces you did not draw: text selection, caret, scrollbars, focus rings,
  underline offset and thickness, tabular numerals in data. They ship with defaults that belong
  to no design system, and theming them is the cheapest signal that a page was built rather
  than assembled.

The mechanical floor (contrast, states, focus, hit targets, responsive, performance, tokens) is
in `references/craft.md`. Load it immediately before writing UI code. It never picks the
direction.

## Motion

You have permission to animate, and a duty to make it mean something. Modern surfaces feel
made partly because their motion is authored: one orchestrated moment the page has earned, plus
micro-interactions that confirm, orient, and reward without ever making the visitor wait.

- Write a motion thesis in one line before building: the focal moment (if any), what needs
  continuity, what needs feedback, and the performance budget.
- Persuade and Experience may carry voice in motion: an entrance choreographed once, a
  signature interaction, scroll-driven reveals where the scroll relationship itself means
  something. Operate and Read get fast feedback and continuity only; no load choreography.
- Reach past transform and opacity when it stays smooth: blur, backdrop, clip-path, mask,
  shadow, color, and variable-font axes are all material. View Transitions, `@starting-style`,
  scroll timelines, and `@property` are native and cost zero bytes; prefer them to a library.
- Defaults: ease-out for anything entering, under 300 ms for UI, exits about 20 percent faster
  than entrances, larger elements move slower, start entrances from scale 0.95 not 0, never
  animate a keyboard-initiated action, never animate a focus ring in.
- Cut before adding. If removing an animation loses no information and no character, remove
  it. The fade-up on every section, the lift on every card, the blob that drifts because
  something had to move: these are what make a page read as generated.

Recipes, timing tokens, and the modern toolkit are in `references/motion.md`. Load it whenever
the surface has interaction or an authored moment, which is most surfaces.

## Words

Copy is design material. Name things what they are, concisely and directly. A headline says
what the thing is and who it is for in plain words; it is not a pun, a koan, a refusal, or a
clever fragment the subhead has to explain. Realistic content, specific verbs, sentence case,
no lorem ipsum, no "Jane Doe", no startup bingo ("seamless", "supercharge", "reimagine",
"built for the modern team"). A control names its action and keeps that name through the flow. Errors say what
happened, why if known, and what to do; they do not apologize or joke. Empty states invite the
next action. See `references/copy.md` when writing more than labels.

## Check, then stop

Build fully, then inspect once with a batched round: desktop and mobile together, real copy at
every breakpoint, keyboard focus, reduced motion. If a browser tool is available, look at the
rendered page; a screenshot is worth a thousand tokens. Settle entrance motion before capturing
so hidden-by-timing elements don't read as missing. Fix everything that round shows in one
batch, confirm with at most one more round, and stop. Endless self-polishing costs the user
money and finds less than a fresh review would.

Before handing back, run the five questions from `references/audit.md` on your own work:
guess-from-category, silhouette, memory, skeleton, and remove-one-accessory. Anything that
fails gets a revision, not a caveat.

Report the direction in three to five lines (world, type, color logic, signature, what to
replace with real material), then the code. No checklist narration, no announcing the floor.

## Refinement verbs

- **bolder**: raise the named target to the expressive level its neighbors already reach, in
  the system's own vocabulary. Reaching for more effects is the opposite of bold. Commit one
  decisive move completely, then quiet everything around it. No new colors, fonts, or
  primitives without asking.
- **quieter**: reduce intensity with precision. Desaturate toward 70 to 85 percent, fewer
  weights, more air, thinner rules, shorter travel. Hierarchy and point of view survive; quiet
  without intent collapses to generic.
- **polish**: refinement, never concealed redesign. Triage in order: broken paths, missing
  states, hierarchy and responsive drift, visual and motion inconsistency, code cleanup. Fix the
  cause at the narrowest level; promote a value to a token only when it is genuinely reused.
- **audit** / **critique**: read only, no edits. Named tells with file and line, severity, and a
  one-line fix. Format in `references/audit.md`.

## References

- `references/tells.md`: the rut mapped by era, with what survived and the fix for each tell.
- `references/type-and-color.md`: choosing faces and building palettes; the spent list.
- `references/motion.md`: principles, tokens, recipes, the native toolkit, reduced motion.
- `references/craft.md`: the mechanical floor to load right before writing UI code.
- `references/copy.md`: voice, labels, errors, empty states, honest content.
- `references/audit.md`: the self-critique questions and the audit report format.
