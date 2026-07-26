---
name: deep-review
description: This skill should be used when the user asks for a "code review", says "review this code", "review these changes", wants to "check for bugs", "look for issues", or asks for architectural feedback on code they've written.
argument-hint: "[files, directories, 'recent changes', or PR number]"
allowed-tools: Read, Grep, Glob, Agent, Bash(git:*), Bash(gh:*)
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
- More than ~20 files: fan the initial reading out to Sonnet subagents (one per module/directory, asking each for structure, responsibilities, and suspicious areas), then personally read the files flagged as critical. Never write findings about code you haven't read directly — subagent summaries identify where to look, not what to conclude.

## Calibrate Depth to Scope

Match the review depth to the size of the scope. Do not run every phase at full depth on every invocation:

| Scope | Layers | Phase 1 evidence | Codex finders | Phase 3 verification |
| ----- | ------ | ---------------- | ------------- | -------------------- |
| **Small** (1–3 files, small diff) | Skip Layer 1 unless the change is itself architectural (new module, new dependency direction, new pattern). Full Layers 2 and 3. | Skip | Skip | Critical/Important only |
| **Medium** (a feature, a directory, a substantial diff) | Brief Layer 1 scoped to the touched area; full Layers 2 and 3. | Run | C1 + C2 | All findings |
| **Large** (full project) | All three layers at full depth. | Run | C1 + C2 + C3 | All findings |

Spinning up eight agents to review one file is worse than reading it yourself — the coordination overhead buys nothing and the evidence agents have no history to find. Match the machinery to the job.

## Delegation Model

Subagents gather and check. You judge. The review has four phases: gather evidence, work the layers, verify every finding, then report.

| Job | Who | Model |
| --- | --- | ----- |
| Evidence gathering (Phase 1) | subagent | `sonnet` |
| Layer 1 — macro architecture | **you** | — |
| Layers 2–3 | **you** | — |
| Technical finders (Phase 2) | Codex | Codex (`--effort xhigh` for C2) |
| Verification (Phase 3) | subagent | **`opus`** |
| Report synthesis (Phase 4) | **you** | — |

Pin the model explicitly on every dispatch. Do not let a subagent inherit whatever the session happens to be running.

Three rules that do not bend:

1. **Verification is Opus. Always.** Never Sonnet, never Haiku, never Codex — not even when every other agent in this review is running Sonnet. Verification is the one step where being wrong is most expensive, because it decides whether a finding reaches the user at all.
2. **Codex finds, Codex never judges.** Codex agents produce candidate findings only. They never verify a finding, never assign a severity, and never touch Layer 1.
3. **Never write a finding about code you have not read yourself.** Subagent output tells you where to look, never what to conclude.

## Diff Reviews: Introduced vs. Pre-existing

When the scope is a diff or PR, tag every finding as one of:

- **Introduced** — caused or made worse by these changes. These drive the verdict.
- **Pre-existing** — already present in the surrounding code. Report these separately and concisely; they inform the author but do not block the change.

A clean change in a messy codebase deserves a clean verdict. Never fail a diff review for sins it didn't commit.

## Phase 1 — Gather Evidence

Run for diff and PR scopes at medium size or larger. Four Sonnet agents in parallel.

These agents return **cited facts, not findings**. A finding requires judgment they are not being asked to exercise. What they produce changes what you look for in the layers; it does not go into the report on its own.

- **Conventions** — the root CLAUDE.md, any CLAUDE.md in directories the change touches, and lint/style config. Return the specific rules that could bear on this change, each quoted with its `file:line`. Not a summary of the file — the applicable rules, verbatim.
- **History** — `git blame` and `git log -L` over the changed line ranges. For each touched region: when it last changed, what the commit said, and whether any prior commit was a fix. A line introduced by `fix: guard against empty batch` is a different line than one introduced by `initial commit`, and simplifying the first one is how a bug comes back.
- **Prior review comments** — merged PRs that touched these files (`gh pr list`), and the review comments left on them. Return any that could apply again. Skip silently if there is no `gh` or no remote.
- **Comments as spec** — doc comments, invariant comments, and TODOs in and around the changed code. Return any that the change could contradict. A comment saying "callers must hold the lock" is a contract, and the diff either honors it or breaks it.

## Phase 2 — Execute the Review Layers

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

### Codex Finders (run in parallel with the layers)

The layers stay yours and stay sequential — Layer 1 is what tells you which paths deserve Layer 3's attention. Do not parallelize them and do not delegate them.

While you work them, dispatch Codex finders alongside. Codex is strong at exhaustive mechanical reasoning — walking every call site, every branch, every boundary value — which is expensive in attention and cheap in judgment. That is the slot it fills.

Dispatch with the `codex:codex-rescue` subagent:

```
Agent(
  subagent_type: 'codex:codex-rescue',
  prompt: '--fresh --wait <task>. Read-only review: do not modify, create, or delete any file.'
)
```

- `--fresh` is required. Without it, parallel Codex agents can resume into each other's thread.
- The read-only sentence is required. The rescue subagent adds `--write` by default.

Which finders to run:

- **C1 — shallow diff scan.** The changed lines only, no wider context. Large, obvious bugs. Deliberately context-starved: it catches the things that deep reading talks itself out of.
- **C2 — technical correctness**, with `--effort xhigh`. Concurrency and ordering, arithmetic and boundary conditions, resource lifecycle, state-machine and protocol correctness.
- **C3 — exhaustive tracing**, large scopes only. Fire this after Layer 2, once you can name a concrete target: "every call site of `parseConfig`, and which ones can reach it with an absent value."

**Never send Codex an architecture question.** Not module boundaries, not dependency direction, not "should this be structured differently", not severity calls, not anything from Layer 1. It answers those with abstraction layers, factories, and config knobs you did not need.

### Handling Codex Output

Codex returns prose, and it is the least trusted input in this review. Normalize it before it touches anything else:

- Every Codex claim enters as a **candidate finding with no severity attached.** You assign severity, after verification.
- **Strip any recommendation that adds an abstraction, indirection layer, config option, or design pattern** unless it fixes a concrete bug you can state as a failure scenario. Over-engineering is Codex's dominant failure mode. Stripping the recommendation does not discard the finding — if it found a real bug and proposed an over-built fix, keep the bug and write your own fix.
- Codex-sourced findings require **two independent Opus verifiers**, both clearing the threshold. Claude-sourced findings require one.

## Classify Findings by Severity and Fix Effort

Every finding gets both. They are independent axes: severity says whether it's worth fixing, effort says what fixing it costs. A Critical one-liner and a Critical redesign call for completely different responses, and a report that only gives severity leaves the reader unable to tell them apart.

Assign both as you draft the finding — Phase 3 gates on severity.

| Severity      | Meaning                                                                                    | Action                    |
| ------------- | ------------------------------------------------------------------------------------------ | ------------------------- |
| **Critical**  | Will cause bugs, data loss, or security vulnerabilities in production.                     | Must fix before shipping. |
| **Important** | Significant design debt, incorrect patterns, or reliability risks that compound over time. | Should fix soon.          |
| **Minor**     | Suboptimal patterns, missed opportunities for clarity, small inconsistencies.              | Fix when convenient.      |
| **Nit**       | Style preferences, naming suggestions, minor readability tweaks.                           | Take or leave.            |

| Fix effort     | Scope of the change                                                                                                    |
| -------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Trivial**    | A line or two. No signature change, no caller touched.                                                                  |
| **Contained**  | A function or two, inside one file. Callers unaffected.                                                                 |
| **Refactor**   | Multiple files, or a changed interface that callers must be updated for. Mechanical once decided, but it spreads.        |
| **Needs plan** | The right fix isn't obvious yet, or it moves a boundary between modules. Requires a design decision before any code.     |

Calibrating effort:

- **Rate by what the fix touches, not by how hard it feels.** The dominant error is optimism — a change that alters a signature is a Refactor even when the edit itself is one word, because every call site is now in scope.
- **"Needs plan" is about uncertainty, not size.** If you can name the fix and it's just large, that's a Refactor. If you'd have to decide something first — which module should own this, whether the abstraction is right — it's Needs plan, and it does not belong in the same work session as the rest.
- **If you can't size it, you may not understand it.** Being unable to state the fix concretely enough to rate it is a signal the finding is under-verified. Sharpen it or move it to Open Questions.

## What Not to Report

These are the recurring false positives. Recognize them during the layers, and reject them again during verification:

- **Pre-existing issues** presented as introduced. Tag them Pre-existing or leave them out.
- **Things that look like a bug but aren't** once you trace the actual call sites.
- **Pedantic nitpicks a senior engineer wouldn't raise.** If it wouldn't survive being said out loud in a review, it doesn't go in.
- **Anything a linter, typechecker, or compiler catches** — missing imports, type errors, formatting. If you spot one, group them into a single line with no severity. Don't spend a finding on it, and don't run the build to hunt for more.
- **Generic quality complaints** — "needs more tests", "could use better docs" — unless the project's own conventions require it or a specific behavior in this change is untested.
- **Rules the code explicitly silenced**, e.g. with a lint-ignore comment. That's a decision, not an oversight.
- **Changes that are likely intentional** or that follow directly from the broader change.
- **Real issues on lines the change didn't touch**, in a diff review. Those are Pre-existing.

## Phase 3 — Verify Every Finding

Every candidate finding is verified before it reaches the report. This includes findings you are confident in — self-assessed confidence is precisely what this phase exists to replace, and the author of a finding is the worst available judge of it.

Dispatch one agent per finding in parallel, each pinned to `model: 'opus'`. Each gets the finding, the file, and the repo. **Never give the verifier your reasoning** — a verifier that sees why you believed something is checking your argument instead of the code. Ask it to refute the finding.

Score 0–100. Give the agent this rubric verbatim:

- **0** — Not confident at all. This is a false positive that doesn't stand up to light scrutiny, or is a pre-existing issue.
- **25** — Somewhat confident. This might be a real issue, but may also be a false positive. You weren't able to verify that it's real. If the issue is stylistic, it is one not explicitly called out in the project's conventions.
- **50** — Moderately confident. You verified this is a real issue, but it might be a nitpick or might not happen often in practice. Relative to the rest of the change, it's not very important.
- **75** — Highly confident. You double checked the issue and verified it is very likely real and will be hit in practice. The existing approach is insufficient. It directly impacts the code's functionality, or it is explicitly called out in the project's conventions.
- **100** — Absolutely certain. You double checked and confirmed this is definitely a real issue that will happen frequently in practice. The evidence directly confirms it.

For findings flagged on a conventions rule, the verifier must confirm the rule actually says that, specifically — not that it gestures in that direction.

Then apply the gate:

| Severity | Report | Demote to Open Questions | Drop |
| -------- | ------ | ------------------------ | ---- |
| Critical / Important | ≥ 80 | 50–79 | < 50 |
| Minor / Nit | ≥ 50 | — | < 50 |

For Codex-sourced findings, both verifiers must clear the bar. If they disagree, take the lower score.

A finding that fails the gate is gone. Do not downgrade its severity to sneak it back in under a lower threshold, and do not mention it in passing in the summary. Dropped means dropped.

## Phase 4 — Structure the Report

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
- **Fix effort** — Trivial, Contained, Refactor, or Needs plan. Required on every finding.
- **Confidence** — Critical and Important findings only: the verification score, and `via Codex` if that's where it originated. Lets the reader calibrate how hard to look before acting.
- **What** — Describe the issue. Show the relevant code snippet (just the essential lines).
- **Why it matters** — The concrete risk or consequence. When proposing a different pattern, explain what makes it better for this specific situation — not just "best practice says so."
- **Suggested approach** — Describe the fix or improvement direction with enough detail to act on. For Needs plan findings, say what has to be decided rather than proposing an implementation.

### 4. Strengths

Call out what is done well. Good architecture, clean patterns, clever solutions, thorough edge case handling — name them explicitly. Every codebase has strengths worth recognizing.

### 5. Open Questions

Two things land here. First, anything ambiguous during the review — intent that's unclear, behavior that could be intentional or accidental, areas where you lacked enough context to be sure. Second, the Critical and Important findings that scored 50–79 in verification: real enough to raise, not solid enough to assert. Phrase those as questions ("is `flush()` guaranteed to run before teardown here?") rather than as softened findings.

This section can be empty if everything was clear and nothing was demoted.

### 6. Verdict

One line. For diff reviews, base it on Introduced findings only. Pick one:

- **Ready to ship** — No critical or important findings. Code is solid.
- **Ready with fixes** — Good overall, but the critical/important findings should be addressed first.
- **Needs rework** — Structural issues that need attention before this is mergeable.

### 7. Summary

Three to five sentences: overall assessment, the most impactful finding, and recommended next steps.

Order the next steps by severity against effort, not severity alone. Critical and Important findings rated Trivial or Contained come first — they're the cheapest risk reduction available and can be done immediately. Call out anything rated **Needs plan** separately, as work that requires a decision before it requires code; folding it into the same list implies it can be knocked out alongside the quick fixes, and it can't.

## Review Principles

- **Follow the data.** Trace values from origin through every transformation to final use. This is the most reliable way to find real bugs.
- **Read first, opine second.** Open every file in scope before writing a single finding. First impressions from file names are often wrong.
- **Confidence is measured, not felt.** A finding you can't defend with a concrete failure scenario is speculation. Phase 3 decides what survives — your own certainty about a finding is not evidence for it. Never manufacture findings to fill the template; a short report on clean code is a successful review.
- **Explain the why.** Never say "use pattern X" without explaining what problem it solves here. A finding without a rationale is just an opinion.
- **Be specific.** Reference exact file paths, line numbers, variable names. Vague findings are not actionable.
- **Calibrate proportionally.** A weekend project and a production system have different quality bars. Match severity to the project's actual context.

## Mindset

Review with the thoroughness of a senior engineer who wants this code to succeed. Be direct about problems — surface them now while they are cheap to fix — and name what is working well. The goal is a codebase that is measurably stronger after this review.
