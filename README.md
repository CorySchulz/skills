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
/chill-programmer
/code-review
/shopify-theme
```

**Codex CLI** — skills use dollar sign syntax:

```
$chill-programmer
$code-review
$shopify-theme
```

---

## Skills

### chill-programmer

Core personality and communication style that layers on top of everything else. Makes your AI assistant talk like a real pair programming partner instead of a corporate help desk.

- Talk like a real person, not a help desk
- Have actual opinions instead of listing 5 options and saying "it depends"
- Skip paranoid edge cases that will never happen
- Keep code simple and elegant — no over-engineering
- Celebrate wins and keep the energy up
- Trust you as a peer, not lecture you as a student

**Trigger:** Always active — applies to every interaction as a personality layer

### code-review

Structured, three-layer code review that actually finds real issues. Reviews in three passes — macro architecture, mid-level design, and micro implementation — then classifies findings by severity (Critical / Important / Minor / Nit). Focuses on bugs that will actually bite you, not stylistic nitpicks.

**Trigger:** Ask for a "code review", say "review this code", "check for bugs", or ask for architectural feedback

### review-with-codex

**Works with:** Claude Code only

Sends your current plan to Codex CLI for a second opinion, then integrates the feedback. Writes the plan to a temp file, runs it through `codex exec` in read-only mode, filters out the over-engineering tendencies, and incorporates the genuinely useful feedback into an updated plan.

**Trigger:** Say "review this with codex" or ask for a "second opinion"

### web-components

Standards and conventions for building Magic Spells web components. Covers class structure (`queryDOM()`, `attachListeners()`, handler patterns), naming conventions, CSS patterns (custom properties, tag-name selectors, attribute selectors for state — no SCSS, no BEM), event naming (`component-name:event` namespace), light DOM communication, the element hierarchy pattern (root, trigger, panel, option, divider, label), accessibility requirements (WAI-ARIA, keyboard nav), and form integration.

**Trigger:** Creating, modifying, or reviewing any custom element in the `@magic-spells` ecosystem

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
