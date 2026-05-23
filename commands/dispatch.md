---
description: Scaffold a compliant worker dispatch file under .reports/dispatches/ that honors The Ai Expert principles (think → simplicity → surgical → goal-driven → verify-behavior → finish-what-you-started).
---

# /dispatch — Scaffold a worker dispatch

Use this to generate a dispatch file your orchestrator hands to a background worker. Fill in the bracketed sections; keep length under ~150 lines.

```markdown
# Dispatch: [one-line title — what shipping looks like]

**Date:** [YYYY-MM-DD]
**Project:** [project name]
**Ticket:** [tracker ID, e.g. LIN-123 / JIRA-456 — if applicable]
**Size:** [XS / S / M / L — your sizing instinct + 1 sentence justification]
**Priority:** [Urgent / High / Medium / Low]
**Author:** [orchestrator name]
**Greenlit by:** [stakeholder + date]

## TL;DR (3-5 sentences max)

[What's broken / missing / desired]. [Why this matters]. [What "done" looks like].

## Scope — in

- [Concrete file path or component] — [what changes]
- [Concrete file path or component] — [what changes]

## Scope — out (do NOT touch)

- [What looks tempting but isn't this dispatch] — [why deferred]

## Pattern source

[If this dispatch replays an existing canonical pattern, point at it explicitly. Read the diff before writing code. Don't re-invent.]

## Workflow

1. **Branch** `[branch-name]` off `main`.
2. **Read** every file path referenced in this dispatch in your session before writing code.
3. **Failing test first** (Principle #4): [name the test file + the contract it pins].
4. **Implementation** — minimum diff that satisfies the acceptance criteria.
5. **`/simplify`** before commit.
6. **Build + lint clean.**
7. **`/adversarial-review`** — Codex pass. Address findings.
8. **PR** with summary + `Verified` and `Unverified — reviewer please decide` sections per Principle #5.
9. **Auto-merge authorised on Codex pass** OR **request reviewer** [pick one + justify if auto-merge].
10. **Live-verify** post-deploy. Bundle hash / API row / Chrome MCP / curl — whichever surface proves user-facing behavior.
11. **Report** at `.reports/[descriptive-name]-[date].md`.
12. **Update tracker** ticket to Done with PR link + report path.

## Acceptance criteria

- [ ] [Specific, testable assertion — not "looks good"]
- [ ] [Specific, testable assertion]
- [ ] Type-check clean
- [ ] Lint clean
- [ ] Failing test passes
- [ ] Adversarial review approved
- [ ] Live-verify evidence captured in report

## Reference files

- [file path] — [why relevant]
- [file path] — [why relevant]

## Six Principles compliance — pre-check (orchestrator self-audit)

- [ ] **#1 Think** — I read every referenced file in this session, not from memory
- [ ] **#2 Simplicity** — Dispatch is under ~150 lines; no abstractions worker didn't need
- [ ] **#3 Surgical** — Every changed line traces to the originating ask
- [ ] **#4 Goal-driven** — Acceptance criteria are testable, not aspirational
- [ ] **#5 Verify behavior** — Live-verify step is named, not assumed
- [ ] **#6 Finish what you started** — If new scope surfaces, spawn a new dispatch; don't interrupt this one
```

## When to use this command

- You are an orchestrator handing scoped work to a worker (background agent, contractor, teammate)
- The work is non-trivial enough to warrant a written brief
- You want the worker to honor the Six Principles by reading them in their context

## When NOT to use this command

- You are doing the work yourself in the same session (skip dispatch, just do it)
- The scope is one trivial change (a hookify rule scaffold beats a dispatch for under-30-LOC patches)
- You haven't actually thought through what "done" looks like yet — go back and think first

## Tied principle

Principle #4 — Goal-driven execution. A dispatch is the artefact that turns "do this thing" into "verify these specific success criteria." If you can't fill in the acceptance criteria above, the work isn't ready to dispatch.
