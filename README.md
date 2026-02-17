# Skills for Claude Code & Codex CLI

A collection of skills that make AI coding assistants less robotic and more useful. Built for [Claude Code](https://claude.ai/code) and [Codex CLI](https://github.com/openai/codex).

---

## What are skills?

Skills are modular instruction sets that change how your AI assistant thinks, writes code, and communicates. Drop them into your skills folder and they activate automatically based on context.

---

## Installation

### Claude Code

Clone this repo into your Claude Code skills directory:

```bash
# Remove the default skills folder if it exists
rm -rf ~/.claude/skills

# Clone directly into the skills path
git clone https://github.com/CorySchulz/skills.git ~/.claude/skills
```

Skills are picked up automatically — no config changes needed. Claude Code reads the `SKILL.md` files and triggers them based on what you're working on.

### Codex CLI

Clone this repo into your Codex skills directory:

```bash
# Remove the default skills folder if it exists (back up first if you have custom skills)
rm -rf ~/.codex/skills

# Clone directly into the skills path
git clone https://github.com/CorySchulz/skills.git ~/.codex/skills
```

Codex reads `SKILL.md` frontmatter to decide when to activate each skill.

### Cherry-pick individual skills

If you only want specific skills, copy the folders you need:

```bash
# Example: just grab pair-programmer and code-review
cp -r pair-programmer ~/.claude/skills/
cp -r code-review ~/.claude/skills/
```

---

## Skills

### pair-programmer

**Works with:** Claude Code, Codex CLI

The anti-corporate personality. Turns your AI into a chill pair programming partner instead of a corporate assistant. It will:

- Talk like a real person, not a help desk
- Have actual opinions instead of listing 5 options and saying "it depends"
- Skip paranoid edge cases that will never happen
- Keep code simple and elegant — no over-engineering
- Celebrate wins and keep the energy up
- Trust you as a peer, not lecture you as a student

### code-review

**Works with:** Claude Code, Codex CLI

Deep, multi-layered code review that actually finds real issues. Reviews in three passes — macro architecture, mid-level design, and micro implementation — then classifies findings by severity. Focuses on bugs that will actually bite you, not stylistic nitpicks.

**Trigger:** Ask for a "code review", say "review this code", or "check for bugs"

### review-with-codex

**Works with:** Claude Code

Sends your current plan to Codex CLI for a second opinion, then integrates the feedback. Useful for getting a sanity check from a different model before committing to an approach. Automatically filters out Codex's tendency to over-engineer everything.

**Trigger:** Say "review this with codex" or ask for a "second opinion"

### shopify-theme

**Works with:** Claude Code, Codex CLI

Complete guide for developing Shopify Liquid sections, snippets, and templates in the Luna theme framework. Covers section anatomy, image rendering, button patterns, color schemes, data-attribute system, web components, and Tailwind utilities.

Includes reference files for:
- Custom Tailwind utilities and spacing
- Tarot carousel configuration
- Magic Spells web component patterns

**Trigger:** "Create a section", "build a snippet", "edit a Liquid file", or any Shopify theme work

### tarot

**Works with:** Claude Code, Codex CLI

Full reference for the Tarot carousel web component system. Covers HTML structure, all configuration options, available effects (carousel, fade, ripple, spotlight, sliding-window), JavaScript API, events, and responsive breakpoints.

**Trigger:** "Add a carousel", "carousel options", "carousel effect", or any carousel-related work

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
