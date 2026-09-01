# Audit and self-critique

Two uses. First, the five questions you run on your own work before handing back. Second, the
report format when the user asks for an audit or critique of an existing surface.

## The five questions (run on every build)

1. **Guess-from-category.** Could someone guess this direction from the category alone, or from
   the category plus what you avoided? If yes, the direction is a default. Rework the axis the
   brief left free.
2. **Silhouette.** Reduce the page to a 200-pixel black silhouette of blocks and lay it beside
   the category's competitors. If you cannot pick yours out, restructure; recoloring will not
   save it.
3. **Memory.** If a visitor left after one viewport, what would they describe an hour later? If
   the honest answer is a mood ("clean", "dark", "editorial") rather than a thing, the concept
   has not committed. If the answer is your signature element, good.
4. **Skeleton.** Strip the copy out. Does the structure still say what this is and why it
   matters, through hierarchy and the system's devices alone? If it only works once the words
   return, the boldness is in the font size, not the design.
5. **Remove one accessory.** Before leaving the house, look in the mirror and take one thing
   off. Find the decoration that is not earning its place and cut it. Then check that the
   signature element still reads louder than everything else.

Score the six axes honestly, one to five: Philosophy (a position, not just a layout),
Hierarchy (primary, secondary, tertiary readable in two seconds), Execution (rules, contrast,
wrap, focus, states in spec), Specificity (looks like this brief, not anyone's), Restraint
(everything remaining earns its place), Variety (structurally distant from the last thing you
built for this user). Anything under three triggers a revision, not a caveat. Two passes is
normal; a third means the brief was misread.

## Audit report format

Read only. Do not edit. Do not redesign. Read the code and, when a browser is available, the
rendered surface at desktop and mobile.

Check three layers:

- **Tells**: every named item in `tells.md`, with genre awareness. A radial bloom is a tell on
  an editorial page and legitimate on a deliberately atmospheric one; pure white paper is a
  tell on a paper world and legitimate on a monochrome instrument world. Judge against the
  world the page committed to, and flag it separately if the page never committed to one.
- **Structure**: the silhouette test, the rhythm down the page, whether the nav and footer
  share the page's voice, whether numbering and labels carry information.
- **Floor**: `craft.md` items that fail: contrast pairs, missing states, focus, hit targets,
  wrap, overflow, LCP, tokens bypassed, reduced motion missing.

For each finding:

```
[critical|major|minor] Tell name — path:line
  why it reads as generated or dated (one line)
  → fix (one line)
```

Then:

```
Structure — <one line: the silhouette and what it resembles>
Summary — N critical · M major · K minor
Verdict — ships as generated | reads as generated | close, fix the majors | made
```

Rank by what a visitor would notice first, not by count. Two majors in one view equal a
critical. When the user asks "is this AI slop?", answer with the verdict line first.

## Usability critique (Operate surfaces)

When the target is a tool rather than a page, add a short heuristic pass: visibility of
status, match to the user's language, control and escape routes, consistency, error
prevention, recognition over recall, accelerators for experts, minimalism, error recovery,
help in context. Score each 0 to 4 only if the user asked for scores; otherwise list the three
findings that would most change task completion, each with the concrete fix.
