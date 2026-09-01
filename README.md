# Skills for Claude Code & Codex CLI

A collection of skills that make AI coding assistants less robotic and more useful. Built for [Claude Code](https://claude.ai/code) and [Codex CLI](https://github.com/openai/codex).

---

## What are skills?

Skills are modular instruction sets that change how your AI assistant thinks, writes code, and communicates. Drop them into your skills folder and they activate automatically based on context.

---

## Installation

Download or clone this repo, then copy the skill folders you want into your skills directory:

- **Claude Code:** `~/.claude/skills/`
- **Codex CLI:** `~/.codex/skills/`

Skills are picked up automatically — no config changes needed.

### Usage

**Claude Code** — skills show up as slash commands:

```
/deep-review
/web-components
/review-with-codex
```

**Codex CLI** — skills use dollar sign syntax:

```
$chill-programmer
$deep-review
```

---

## Skills

### tasteful-frontend

The design skill. Combines the best of Anthropic's frontend-design, Impeccable, and Hallmark into one focused, portable skill with no scripts or ceremony. Maps the "AI slop" tells by era (including the 2024–26 anti-slop defaults that became the new slop), forces a decision instead of a default (derive a world from the subject, commit to a thesis, signature, and first viewport), enforces structural variety with the silhouette test, gives real permission for modern motion (View Transitions, scroll-driven animation, `@starting-style`, micro-interactions) with the discipline to keep it tasteful, and ships a mechanical craft floor, a type and color guide with the "spent" font list, a copy guide, and an audit format.

**Trigger:** Any front-end visual work — building or redesigning pages, screens, or components; type, color, layout, motion, or UX copy; "make it look good", "this looks like a template", "add some animation", "polish this", or `/tasteful-frontend audit <target>`

### deep-review

Structured, three-layer code review that actually finds real issues. (Formerly named `code-review` — renamed to avoid shadowing Claude Code's built-in `/code-review` command.) Reviews in three passes — macro architecture, mid-level design, and micro implementation — then classifies findings by severity (Critical / Important / Minor / Nit). Focuses on bugs that will actually bite you, not stylistic nitpicks.

**Trigger:** Ask for a "code review", say "review this code", "check for bugs", or ask for architectural feedback

### web-components

Standards and conventions for building Magic Spells web components. Covers class structure (`queryDOM()`, `attachListeners()`, handler patterns), naming conventions, CSS patterns (custom properties, tag-name selectors, attribute selectors for state — no SCSS, no BEM), event naming (`component-name:event` namespace), light DOM communication, the element hierarchy pattern (root, trigger, panel, option, divider, label), accessibility requirements (WAI-ARIA, keyboard nav), and form integration.

**Trigger:** Creating, modifying, or reviewing any custom element in the `@magic-spells` ecosystem

### chill-programmer

Designed for Codex CLI to give GPT a better personality. Claude already has a great personality — this one's for making Codex talk like a real pair programming partner instead of a corporate help desk. Have actual opinions, skip paranoid edge cases, keep code simple, and trust you as a peer.

**Trigger:** Always active — applies to every interaction as a personality layer

### review-with-codex

Designed for Claude Code to communicate with Codex CLI. Sends your current plan to Codex for a second opinion, then integrates the feedback. Writes the plan to a temp file, runs it through `codex exec` in read-only mode, filters out the over-engineering tendencies, and incorporates the genuinely useful feedback into an updated plan. Assumes Codex CLI is installed with an active account and subscription.

**Trigger:** Say "review this with codex" or ask for a "second opinion"

---

## Updating

```bash
cd ~/.claude/skills && git pull
```

---

## Making your own skills

Each skill is just a folder with a `SKILL.md` file. The frontmatter (`name` + `description`) tells the AI when to use it, and the markdown body contains the actual instructions.

```
my-skill/
├── SKILL.md          # Required — frontmatter + instructions
└── references/       # Optional — extra docs loaded on demand
```

Minimal example:

```markdown
---
name: my-skill
description: When to trigger this skill — be specific about keywords and contexts.
---

# My Skill

Instructions go here. Write them like you're onboarding a smart coworker
who just needs context, not a tutorial.
```
