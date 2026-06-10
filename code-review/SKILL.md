---
name: code-review
description: This skill should be used when the user asks for a "code review", says "review this code", "review these changes", wants to "check for bugs", "look for issues", or asks for architectural feedback on code they've written.
argument-hint: "[files, directories, 'recent changes', or PR number]"
allowed-tools: Read, Grep, Glob, Task, Bash(git:*), Bash(gh:*)
---

# Code Review

Perform a layered code review of the specified scope. The output is a findings report — do not edit any files.

Scope requested: $ARGUMENTS

## Determine Scope

Interpret the requested scope:

- **Specific files or directories** — Review those files and their immediate collaborators (imports, callers, interfaces they implement).
- **"recent work" / "my changes" / branch name** — Diff against the base branch. Detect the base rather than guessing: try `git merge-base HEAD origin/main`, fall back to `origin/master`, then `@{upstream}`. Review all changed files plus any files they directly depend on or that depend on them. This is a **diff review** (see below).
- **A PR number or URL** — Fetch the diff with `gh pr diff` and the description with `gh pr view`. Review all changed files in full, not just the diff hunks — bugs often live in the unchanged lines the diff interacts with. This is also a **diff review**.
- **"full project" / "everything" / no arguments** — If there are uncommitted or unpushed changes, treat those as the scope (diff review). Otherwise start from entry points (build manifest, index/main modules), map the dependency graph outward, and review the full codebase in passes.

Skip generated and vendor artifacts by default — `dist/`, `build/`, `node_modules/`, `vendor/`, lock files, minified bundles, source maps, and generated code are not useful review targets. If a generated file is explicitly passed as an argument, review it anyway.

Read the actual source code. Never form opinions from file names alone. Open each file, read it, trace the data paths.

If the project has a CLAUDE.md, README, or lint/style config with architectural context or conventions, read it first to calibrate expectations — suggestions should match how this codebase already does things.

If a tool isn't available (no git repo, `gh` not installed, PR fetch fails), don't bail — fall back gracefully. No git? Do a path-based review. No `gh`? Ask for a local branch diff instead. Always tell the user what was skipped and why.

### Managing large scopes

- 5–20 files: read them yourself in parallel batches.
- More than ~20 files: fan the initial reading out to subagents (one per module/directory, asking each for structure, responsibilities, and suspicious areas), then personally read the files flagged as critical. Never write findings about code you haven't read directly — subagent summaries identify where to look, not what to conclude.

## Calibrate Depth to Scope

Match the review depth to the size of the scope. Do not run every layer at full depth on every invocation:

- **Small (1–3 files or a small diff)** — Skip Layer 1 unless the change itself is architectural (new module, new dependency direction, new pattern). Spend the effort on Layers 2 and 3.
- **Medium (a feature, a directory, a substantial diff)** — Brief Layer 1 pass scoped to the touched area; full Layers 2 and 3.
- **Large (full project)** — All three layers at full depth.

## Diff Reviews: Introduced vs. Pre-existing

When the scope is a diff or PR, tag every finding as one of:

- **Introduced** — caused or made worse by these changes. These drive the verdict.
- **Pre-existing** — already present in the surrounding code. Report these separately and concisely; they inform the author but do not block the change.

A clean change in a messy codebase deserves a clean verdict. Never fail a diff review for sins it didn't commit.

## Execute the Review Layers

Work through each layer sequentially. Each layer builds on the understanding gained from the previous one.

The checklists below name common failure modes, not an exhaustive or language-specific list. Adapt them to the language and stack under review — every language has its own idioms and failure modes (e.g., nil maps and goroutine leaks in Go, mutable default arguments in Python, lifetime and unwrap issues in Rust, undefined/this binding in JavaScript).

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
- **Test coverage of the behavior** — Do tests exist for the code under review? Did behavior change without any test changing? Are the critical paths and failure paths tested, or only the happy path? Untested behavioral change in a diff is a finding, not a footnote.

### Layer 3 — Micro Implementation

Zoom all the way in. Read line by line through the critical paths identified in the previous layers.

Examine:

- **Edge cases** — Empty collections, zero values, negative numbers, boundary values (first/last item), single-element cases, absent values where one is expected.
- **Off-by-one errors** — Array indexing, loop bounds, range calculations, pagination math, modulo arithmetic on indices.
- **Null/absent-value paths** — Trace what happens when optional values are missing: property/field access on possibly-absent objects, missing nil/None/undefined checks, destructuring or unpacking that assumes presence.
- **Race conditions and timing** — Concurrent operations that assume ordering, state read after an await/suspend point that may be stale, callbacks that reference destroyed state, handlers that fire during initialization, unsynchronized shared mutation.
- **Type coercion and comparison** — Loose equality where strict is needed, string/number confusion, falsy/zero-value gotchas, float comparison, integer overflow or truncation.
- **Resource cleanup** — Listeners not removed, intervals/timeouts not cleared, file handles/connections/locks not released on error paths, subscriptions or goroutines/tasks that outlive their owner.
- **Security surface** — Untrusted input reaching an interpreter or renderer: SQL/command/template injection, path traversal, unsanitized HTML or URL construction, unsafe deserialization, secrets or credentials committed in code, regex denial-of-service, missing authorization checks on sensitive operations.

## Classify Findings by Severity

| Severity      | Meaning                                                                                    | Action                    |
| ------------- | ------------------------------------------------------------------------------------------ | ------------------------- |
| **Critical**  | Will cause bugs, data loss, or security vulnerabilities in production.                     | Must fix before shipping. |
| **Important** | Significant design debt, incorrect patterns, or reliability risks that compound over time. | Should fix soon.          |
| **Minor**     | Suboptimal patterns, missed opportunities for clarity, small inconsistencies.              | Fix when convenient.      |
| **Nit**       | Style preferences, naming suggestions, minor readability tweaks.                           | Take or leave.            |

## Structure the Report

Scale the report to the scope. For small scopes (1–3 files), keep it lean — skip the Architecture Overview and Open Questions sections if there's nothing meaningful to say; Findings and Verdict are what matter. An empty severity level is a valid result — say "no critical findings" and move on rather than padding the section.

### 1. Scope Summary

Two to three sentences: what was reviewed, how many files, which area of the codebase.

### 2. Architecture Overview

One paragraph describing the macro-level architecture as understood from reading the code. Establish shared context before diving into findings.

### 3. Findings

Group by severity (Critical first, then Important, Minor, Nit). Within each group, order by review layer (Macro → Mid → Micro). For diff reviews, list Introduced findings first and collect Pre-existing ones under their own heading.

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

One line. For diff reviews, base it on Introduced findings only. Pick one:

- **Ready to ship** — No critical or important findings. Code is solid.
- **Ready with fixes** — Good overall, but the critical/important findings should be addressed first.
- **Needs rework** — Structural issues that need attention before this is mergeable.

### 7. Summary

Three to five sentences: overall assessment, the most impactful finding, and recommended next steps.

## Review Principles

- **Follow the data.** Trace values from origin through every transformation to final use. This is the most reliable way to find real bugs.
- **Read first, opine second.** Open every file in scope before writing a single finding. First impressions from file names are often wrong.
- **Report only what you're confident in.** A finding you can't defend with a concrete failure scenario is speculation — verify it against the code or put it in Open Questions. Never manufacture findings to fill the template; a short report on clean code is a successful review.
- **Explain the why.** Never say "use pattern X" without explaining what problem it solves here. A finding without a rationale is just an opinion.
- **Be specific.** Reference exact file paths, line numbers, variable names. Vague findings are not actionable.
- **Calibrate proportionally.** A weekend project and a production system have different quality bars. Match severity to the project's actual context.

## Mindset

Review with the thoroughness of a senior engineer who wants this code to succeed. Be direct about problems — surface them now while they are cheap to fix — and name what is working well. The goal is a codebase that is measurably stronger after this review.
