---
name: codex-review-hybrid-policy
description: When to use `/adversarial-plan` vs `/adversarial-review` vs your own multi-iteration Claude→Codex loop. Decision matrix + tradeoffs. Use this skill when planning a code change, before opening a PR, or when deciding which review tier applies.
---

# Codex review — hybrid policy

There are three distinct ways to run adversarial code review against AI-authored work. Picking the right one per task class is itself a discipline. This skill captures the decision matrix.

All three rely on the [`adversarial-review` plugin](https://github.com/claude-code-plugins/claude-community) (from the `claude-community` marketplace) — install it first if you haven't already.

## Decision matrix

| Task class | Process | Why |
|---|---|---|
| **Spec / plan phase** (new ticket → first dispatch, no implementation yet) | **`/adversarial-plan <task>`** | Claude writes plan → Codex reviews as senior staff engineer → iterates to convergence → **pauses for human approval** before implementation. Genuinely useful for spec alignment. |
| **Tactical code PR — no UX, well-defined** (bug fix, refactor, internal logic, predicate rewrite) | **`/adversarial-review`** | Captures git diff → Codex reviews → Claude autonomously fixes critical issues → loops to APPROVED. ~6-8 manual orchestration steps collapse to 1 command. Auto-applies fixes (no human pause). |
| **Substantive PR with mobile-monitoring needed** (long-running work, multi-day, watched from phone via Claude Agent View) | **Your own pattern:** `claude --bg` worker + Codex CLI in tmux (or equivalent) | Preserves Agent View visibility, parallel workstreams, custom dispatch context, per-project guardrails. Slower but more observable. |
| **UX/UI-visible PR** (anything user-sees) | **Your own pattern** | Preserves the "human gates major UX before merge" rule (`adversarial-review` auto-applies code-review fixes, which would skip the gate). |

## What each pattern looks like in practice

### `/adversarial-plan <task description>`

1. Claude analyzes codebase + writes plan to `.adversarial-review/<session-slug>/plan_v1.md`
2. Codex reviews critically, outputs `APPROVED` or `NEEDS_REVISION`
3. If `NEEDS_REVISION` → Claude revises → `plan_v2.md` → Codex re-reviews
4. Loop to convergence (typically 2-3 iters; 20-iter hard cap)
5. Final plan saved as `plan_final.md`
6. **Pauses for human approval before implementing**

Use for: specs that benefit from "review before code."

### `/adversarial-review` (on current git diff)

1. Captures `git diff` of all uncommitted changes
2. Codex reviews for bugs / security / performance / style
3. Claude autonomously fixes critical issues
4. Loop to `APPROVED` (no human pause — auto-apply)

Use for: tactical PRs you're already mid-implementation on. Faster than the manual loop.

### Your own pattern (`claude --bg` worker + codex tmux)

1. Write a dispatch file (`/dispatch` scaffolds one for you — see `commands/dispatch.md`)
2. Fire `claude --bg` worker — worker auto-worktrees + commits + pushes + opens PR
3. Spawn a separate Codex CLI session (tmux or equivalent)
4. Send review prompt referencing PR + dispatch + specific scrutiny areas
5. Codex returns verdict
6. If FAIL → write iter-N fix-up dispatch → fire iter-N worker → re-prompt Codex
7. Loop to convergence
8. Merge per your auto-merge / human-gate convention

Use for: long-running work, work that benefits from custom rubric (e.g., the Six Principles checklist in the dispatch), work that needs mobile-monitoring via Agent View.

## Artifact storage

| Pattern | Artifacts land at |
|---|---|
| `/adversarial-plan` | `<project>/.adversarial-review/<session-slug>/plan_v1.md`, `review_v1.md`, ..., `plan_final.md` |
| `/adversarial-review` | `<project>/.adversarial-review/<session-slug>/diff_for_review.md`, `code_review_v1.md`, ... |
| Your own pattern | Worker report at `<project>/.reports/<descriptive>-<date>.md`; Codex output captured from the CLI session |

## Gotchas

- **`/adversarial-review` locks into Claude's same-session for fixes** — loses external mobile-monitoring benefits. If you need to watch from a phone, your own pattern still wins.
- **`/adversarial-review` prompts are generic** (skill-defined). Custom rubric (e.g., naming the Six Principles, file:line citations, Verified/Unverified PR body shape) requires editing the skill's `scripts/codex-review.sh` or sticking with your own pattern.
- **20-iter cap on `/adversarial-review`** is a backstop. If a review hits it, escalate — usually means the scope is too broad.

## Decision flow when starting work

1. Is it a spec/plan that needs design alignment? → `/adversarial-plan`
2. Is it a tactical code change with no UX and you're ready to converge fast? → `/adversarial-review`
3. Does someone need to watch from a phone, or is it long-running multi-channel work? → your own pattern
4. Is the change visibly UX-impacting? → your own pattern (preserves the human gate)

When in doubt: your own pattern is more conservative (more humans-in-loop, slower, preserves more invariants). `/adversarial-review` is faster but auto-applies fixes.

## Tied principle

Principle #2 — **Simplicity first.** Use the lightest review tier that produces the safety you need. Don't run a multi-day worker loop on a one-line typo fix; don't run `/adversarial-review` on a spec that hasn't been written yet. Match review weight to scope weight.
