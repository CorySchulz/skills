---
name: code-review
description: This skill should be used when the user asks for a "code review", says "review this code", "review these changes", wants to "check for bugs", "look for issues", or asks for architectural feedback on code they've written.
argument-hint: "[files, directories, 'recent changes', or PR number]"
---

# Code Review

Perform a comprehensive, layered code review of the specified scope.

## Determine Scope

Interpret $ARGUMENTS to determine what to review:

- **Specific files or directories** — Review those files and their immediate collaborators (imports, callers, interfaces they implement).
- **"recent work" / "my changes" / branch name** — Run `git diff` against the base branch to identify changed files. Review all changed files plus any files they directly depend on or that depend on them.
- **"full project" / "everything" / no arguments** — Start from entry points (package.json main/exports, index files, main modules), map the dependency graph outward, and review the full codebase in passes.
- **A PR number or URL** — Fetch the PR diff with `gh` and review all changed files in context.

Skip generated and vendor artifacts by default — `dist/`, `build/`, `node_modules/`, lock files, minified bundles, and source maps are not useful review targets. If a generated file is explicitly passed as an argument, review it anyway.

Read the actual source code. Never form opinions from file names alone. Open each file, read it, trace the data paths. When the scope includes more than 5 files, read them in parallel batches for efficiency.

If the project has a CLAUDE.md or README with architectural context, read it first to calibrate expectations before diving into source files.

If a tool isn't available (no git repo, `gh` not installed, PR fetch fails), don't bail — fall back gracefully. No git? Do a path-based review. No `gh`? Ask for a local branch diff instead. Always tell the user what was skipped and why.

## Execute Three Review Layers

Work through each layer sequentially. Each layer builds on the understanding gained from the previous one.

### Layer 1 — Macro Architecture

Zoom out. Map the system from above before touching any details.

Examine:

- **Module boundaries** — Are responsibilities cleanly separated? Does each module/class/file own a single coherent concept, or are concerns bleeding across boundaries?
- **Dependency direction** — Do dependencies flow in a healthy direction (concrete depends on abstract, not the reverse)? Identify any circular dependencies or inappropriate coupling.
- **Separation of concerns** — Is business logic tangled with presentation, I/O, or framework glue? Are side effects isolated from pure computation?
- **Architectural patterns** — Identify the patterns in use (pub/sub, dependency injection, state management, plugin systems, etc.). Assess whether they are applied consistently or if some areas deviate without good reason.
- **Extension points** — Is the system designed to grow? Where would a new feature slot in cleanly, and where would it require surgery?
- **Missing abstractions** — Look for repeated structural patterns that suggest a missing shared abstraction, base class, or utility.

### Layer 2 — Mid-Level Design

Zoom into the boundaries between components. This is where integration bugs and design debt accumulate.

Examine:

- **API design** — Are function/method signatures clear, minimal, and hard to misuse? Look for boolean traps, overly wide parameter types, or APIs that require the caller to know internal details.
- **State management** — Trace how state is created, mutated, and read. Identify state that is duplicated, can become stale, or mutates outside the intended pathway.
- **Data flow across boundaries** — Follow data as it crosses module/class/function boundaries. Look for unnecessary transformations, lost context, or raw data leaking through abstraction layers.
- **Error handling strategy** — Is error handling consistent? Are errors caught at the right level? Look for swallowed errors, overly broad catch blocks, and missing error paths.
- **Event/callback management** — Check for proper cleanup of listeners, potential memory leaks from retained references, and event handlers that fire on stale state.
- **Configuration and defaults** — Examine how options/config flow through the system. Look for magic values, undocumented defaults, or config validated too late.

### Layer 3 — Micro Implementation

Zoom all the way in. Read line by line through the critical paths identified in the previous layers.

Examine:

- **Edge cases** — Empty collections, zero values, negative numbers, boundary values (first/last item), single-element cases, undefined/null where a value is expected.
- **Off-by-one errors** — Array indexing, loop bounds, range calculations, pagination math, modulo arithmetic on indices.
- **Null/undefined paths** — Trace what happens when optional values are absent. Property access on potentially undefined objects, missing nullish checks, destructuring that assumes properties exist.
- **Race conditions and timing** — Async operations that assume ordering, state reads after await that may be stale, animation frame callbacks that reference destroyed state, event handlers that fire during initialization.
- **Type coercion and comparison** — Loose equality where strict is needed, string/number confusion, falsy value gotchas (0, empty string, NaN).
- **Resource cleanup** — Listeners not removed on destroy, intervals/timeouts not cleared, DOM references retained after removal, subscriptions that outlive their owner.
- **Security surface** — innerHTML with user input, unsanitized URL construction, prototype pollution vectors, regex denial-of-service patterns.

## Classify Findings by Severity

| Severity      | Meaning                                                                                    | Action                    |
| ------------- | ------------------------------------------------------------------------------------------ | ------------------------- |
| **Critical**  | Will cause bugs, data loss, or security vulnerabilities in production.                     | Must fix before shipping. |
| **Important** | Significant design debt, incorrect patterns, or reliability risks that compound over time. | Should fix soon.          |
| **Minor**     | Suboptimal patterns, missed opportunities for clarity, small inconsistencies.              | Fix when convenient.      |
| **Nit**       | Style preferences, naming suggestions, minor readability tweaks.                           | Take or leave.            |

## Structure the Report

For small scopes (1-3 files), keep the report lean — skip the Architecture Overview and Open Questions sections if there's nothing meaningful to say. The Findings and Verdict are what matter most on a quick review. Scale the report to match the scope.

### 1. Scope Summary

Two to three sentences: what was reviewed, how many files, which area of the codebase.

### 2. Architecture Overview

One paragraph describing the macro-level architecture as understood from reading the code. Establish shared context before diving into findings.

### 3. Findings

Group by severity (Critical first, then Important, Minor, Nit). Within each group, order by review layer (Macro → Mid → Micro).

For each finding:

- **Title** — Short, specific (not vague like "improve error handling").
- **Location** — File path and line number or function name.
- **What** — Describe the issue. Show the relevant code snippet (just the essential lines).
- **Why it matters** — The concrete risk or consequence. When proposing a different pattern, explain what makes it better for this specific situation — not just "best practice says so."
- **Suggested approach** — Describe the fix or improvement direction with enough detail to act on.

### 4. Strengths

Call out what is done well. Good architecture, clean patterns, clever solutions, thorough edge case handling — name them explicitly. Every codebase has strengths worth recognizing.

### 5. Open Questions

If anything was ambiguous during the review — intent that's unclear, behavior that could be intentional or accidental, areas where you lacked enough context to be sure — list them here. Flag what you're unsure about rather than guessing. This section can be empty if everything was clear.

### 6. Verdict

One line. Pick one:
- **Ready to ship** — No critical or important findings. Code is solid.
- **Ready with fixes** — Good overall, but the critical/important findings should be addressed first.
- **Needs rework** — Structural issues that need attention before this is mergeable.

### 7. Summary

Three to five sentences: overall assessment, the most impactful finding, and recommended next steps.

## Review Principles

- **Follow the data.** Trace values from origin through every transformation to final use. This is the most reliable way to find real bugs.
- **Read first, opine second.** Open every file in scope before writing a single finding. First impressions from file names are often wrong.
- **Explain the why.** Never say "use pattern X" without explaining what problem it solves here. A finding without a rationale is just an opinion.
- **Be specific.** Reference exact file paths, line numbers, variable names. Vague findings are not actionable.
- **Calibrate proportionally.** A weekend project and a production system have different quality bars. Match severity to the project's actual context.

## Mindset

Approach this review with the confidence and thoroughness of a senior architect who genuinely wants this code to succeed. Be direct about problems — surface them now while they are cheap to fix, not later when they compound. Recognize strong work and name what is working well. The goal: a codebase that is measurably stronger after this review. Dig in, follow the threads, find what matters. Let's go.

$ARGUMENTS
