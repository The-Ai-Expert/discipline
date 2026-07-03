---
name: layered-orchestration
description: The two-layer agentic team model - per-project orchestrators dispatching workers, with a lead orchestrator above them owning completeness and protocol rather than dispatch. Use when standing up a multi-agent delivery team, or when deciding who owns what across orchestration layers.
---

# Layered orchestration

Once you run more than one agent on more than one workstream, "who is responsible for what" stops being obvious. This skill describes the two-layer model that keeps a fleet of agents honest without a human babysitting every dispatch.

There are three roles. Two of them orchestrate. One of them executes.

---

## The worker

A worker executes exactly one scoped task and then stops.

- Reads the dispatch and every file it references before writing a line (Principle #1).
- Writes the failing test first, then the minimum diff that passes it (Principles #4 and #2).
- Touches only what the task requires (Principle #3).
- Self-verifies with concrete evidence, not adjectives: the command output, the cropped screenshot, the API row. "Looks right" is not evidence.
- Reports what it verified and what it did not (Principle #5), then hands back.

A worker never picks up new scope mid-flight. If it discovers adjacent work, it names it in the report and leaves it for a fresh dispatch.

---

## The per-project orchestrator

One orchestrator per engagement or workstream. Its job is to convert intent into finished, verified work:

- Owns the backlog for its project and nothing else. It does not reach into another project's work.
- Turns an approved spec into scoped tasks with testable acceptance criteria (`/dispatch` scaffolds the artefact).
- Dispatches workers and supervises them to completion.
- Does not do the coding itself. If it finds itself editing project code, it has collapsed a layer and lost the supervision it exists to provide.

The per-project orchestrator is the first line that verifies a worker's claims against artefacts. A worker saying "done, tests green" is a claim, not a fact, until the orchestrator has seen the evidence.

---

## The lead orchestrator

Above the per-project orchestrators sits one lead. Its job is emphatically **not** dispatching work. Its job is completeness and protocol:

- Tracks what was promised against what actually landed. The gap between the two is where delivery quietly rots.
- Checks results against the principles, not just against "did a PR merge".
- Maintains the watchlists: the open decisions, the verify-debt ledger (see `verify-debt-ledger`), the in-flight workers, the blocked items.
- Is the single human interface. It surfaces to the person only the decisions that genuinely need a person, and absorbs everything else. A good lead makes the human's inbox shorter, not longer.

The lead is a supervisor of supervisors. When it starts writing dispatches itself, it has become a per-project orchestrator and left the completeness job unowned.

---

## The rules that hold the layers together

**No layer blindly trusts the layer below it.** Verify claims against artefacts. A worker's "done" is checked by its orchestrator; an orchestrator's "shipped" is checked by the lead. Trust is earned per-claim, not granted per-role. This is Principle #5 applied to the org chart: the report is a proxy; the artefact is the behaviour.

**Orchestrators never interrupt in-flight workers.** Once a dispatch is fired, the worker owns that scope until done or blocked. New scope does not become a "one more thing" ping; it becomes a new dispatch to a new worker. Interrupting mid-flight means nothing finishes cleanly (Principle #6).

**No agent self-merges schema, CI, or infrastructure changes.** Anything that can break the shared substrate for everyone else goes through an independent adversarial gate before it lands. The author of a migration is the worst-placed party to approve it. See `codex-review-hybrid-policy` for the review tiers.

**One director per orchestrator at a time.** Two people (or two agents) steering the same orchestrator in the same window produces contradictory instructions and half-finished work. Avoid two conductors. If a second stream of scope arrives, it gets its own orchestrator or its own dispatch, not a second voice in the current one.

---

## Why two layers and not one

A single orchestrator that both dispatches and tracks completeness will, under load, always choose dispatching. Dispatching feels productive; tracking completeness feels like overhead right up until the moment something promised three days ago turns out never to have shipped. Splitting the roles means the completeness job has a dedicated owner who is not tempted to drop it for the next shiny task.

The cost is one more layer of indirection. The payoff is that nothing falls through the cracks silently, because catching the things that fall through the cracks is somebody's actual job.

---

## Tied principles

- **#5 Verify the behaviour, not the proxy** - every layer verifies the one below against artefacts, never against its self-report.
- **#6 Finish what you started** - the no-interrupt rule and the "spawn new scope, do not amend" rule are Principle #6 expressed across agents.
- **#2 Simplicity first** - do not add a layer you do not need. One project with one workstream does not need a lead orchestrator. Add the layer when the number of concurrent workstreams makes completeness a full-time concern, not before.
