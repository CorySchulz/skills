---
name: review-with-codex
description: Sends the current plan to OpenAI Codex CLI for a second opinion. Use when a plan is ready for review, when the user asks for a second opinion, or says "review this with codex".
---

Write the current plan to /tmp/plan-review.md as a clean markdown file.

Then run the following command to send the plan to Codex for review:

```
codex exec -c 'sandbox_permissions=["disk-full-read-access"]' "Read /tmp/plan-review.md carefully. Do NOT modify any files. Do NOT create any files. Do NOT write any code. Your only job is to review this plan and return feedback. Review the plan for: gaps in logic, missing edge cases, potential risks, unclear requirements, better approaches, and any improvements. Return an upgraded version of the plan as structured markdown to stdout."
```

Read the output from Codex carefully. Compare it against the original plan. If Codex raised valid concerns or suggested meaningful improvements, incorporate them into an updated version of the plan. If the feedback is minor or unhelpful, note that and keep the original plan mostly intact.

Note: ChatGPT tends to over-engineer solutions — it will suggest formal state machines, extensive validation, and edge cases that may never actually happen in practice. Take this into account when evaluating the feedback. Filter for genuinely useful improvements and discard suggestions that add complexity without real-world benefit.

After reading the Codex output, delete the temporary file:

```
rm /tmp/plan-review.md
```

Present the final upgraded plan for approval with a brief summary of what changed based on the Codex review.

$ARGUMENTS
