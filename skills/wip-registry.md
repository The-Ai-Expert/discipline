---
name: wip-registry
description: Derive-don't-trust work tracking - a cross-project WIP registry reconciled against source control and code consumption, never against labels or self-reports. Catches abandoned-at-N% work (merged but never wired), false "Done" labels, AND its own false alarms. Includes the grounding gate before net-new design and the PM boundary (feed priorities, never dispatch). Use when tracking work across multiple projects/agents, before starting any net-new design, or when a status claim needs checking.
---

# The WIP registry: derive, don't trust

The founding incident: a feature was built, merged, and marked "Done". Then nothing touched it for nineteen days — not because anyone dropped it, but because **nothing in the process would have said it had stalled**. The tracker said finished; the truth was ~70% — merged but never wired into anything. It was rediscovered by accident while scoping unrelated work that nearly rebuilt it from scratch.

The fix is a registry whose every row is **derived from ground truth**, never taken from a label or a self-report.

## The schema

One row per work item: `project · item · ticket · artifacts (branches/PRs/migrations/files) · true % + evidence · last activity · consumed? · status`.

**Status rules:**
- `shipped` = merged, **consumed** (something actually calls/renders/uses it), *and* behaviour-verified live — or explicitly carrying verify-debt for the live check (see the `verify-debt-ledger` skill). Merged-with-a-failed-deploy is not shipped.
- `in-flight` = active artifacts + recent activity
- `on-deck` = specced, no artifacts yet
- `stalled-N%` = **artifacts exist + no recent activity + not delivering value** — whether the missing step is consumption (merged-but-never-wired) or delivery (merged-and-consumed but the deploy/live check failed and went quiet). Stalled is about *stopped short of value with no motion*, at whatever stage it stopped — regardless of what the ticket label says

## How it stays honest

- **Source control over tickets**: merged-PR state, branch activity, commit dates. Tickets are reconciled *against* this, never trusted alone. (Full-text ticket search for "stalled" finds nothing; the founding case was only ever findable via git.)
- **Code-grounding for "consumed?"**: is the new table referenced by application code? Is the new module imported anywhere outside its own tests? *Merged ≠ consumed* — a production-quality subsystem reachable only from its test suite is a 40% item wearing a 100% label.
- **Claims get the registry treatment.** "We're done" from any agent or teammate is checked against the artifact: is it on the default branch? A finished feature on an unpushed local branch is done-in-substance, not done. (This is not distrust of people; it's that "done" honestly means different things at different desks.)

## The method corrects itself both ways

Derive-don't-trust also catches **false stalls**. Real case: a feature was flagged stalled because a generated type-helper file contained no references to its new tables — but the helper was simply stale (last regenerated before the change); the tables were consumed via direct SQL. Deeper grounding cleared the flag. Lesson: **a generated artifact's staleness is not evidence of non-consumption** — ground "consumed?" in actual references, not in derived files. Publish the correction as visibly as the original flag; a tracker that only ever adds red flags teaches people to ignore it.

## The grounding gate (preventive twin)

Before any **net-new design, spec, or greenfield build**: run the check — *does this already exist? did we start it? how done is it?* If prior work exists, the initiator must reckon with it before designing fresh. Scope this to net-new only; a grounding gate on every small fix is friction without value.

## The PM boundary

If a PM-role agent maintains the registry and a board: it **feeds priorities into** the executing orchestrators and **never dispatches work itself**. Execution ownership stays in one place per lane; the PM is a conduit and a truth-keeper, not a second boss. Crossing this stream produces two masters and no accountability.

## Cadence

Weekly full re-derive (fan out one auditor per project; read-only) + cheap continuous drift-checks (does the board's cited PR state still match reality?). Publish the board where the whole team sees it; the *stalled* lane is the entire point — surface it loud.
