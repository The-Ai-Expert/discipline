---
name: retro-flywheel
description: The self-improving loop - every session emits a short friction retro, a weekly pass distils recurring items into permanent fixes (guards, skills, scripts), and adoption/impact are tracked deterministically. The goal state is fixes propagating same-day from one lane's incident to every lane's standard. Use at the end of any substantive session, when the same friction appears twice, or when standing up team process-improvement.
---

# The retro flywheel

Process improves at the speed you convert friction into infrastructure. The flywheel is three moving parts and one standard of proof.

## Part 1 — emit (every session)

At the end of a substantive session, write a four-line retro to a known location (e.g. `.reports/retros/<date>-<session>.md`):

- **Worked**: what went smoothly that's worth keeping
- **Friction**: what cost time that shouldn't have
- **Missing**: the tool/doc/guard that would have prevented the friction
- **Improve**: the concrete change to make

Keep it honest and small. "None observed" is a valid entry; a padded retro is worse than none.

## Part 2 — ingest (weekly)

A weekly pass sweeps all retros and distils **recurring** items into permanent fixes:

- recurring friction → a helper script or a deterministic guard
- recurring confusion → a skill/doc update
- recurring defect class → a gate (see `deterministic-first` — this is the same flywheel)

Mark items ingested so recurrence is measured across weeks, not re-counted. One-off gripes don't earn infrastructure; the tally is what justifies the build.

## Part 3 — track adoption and impact deterministically

A fix that shipped but isn't used is a stalled 40% item (see `wip-registry`). Track adoption by artifact, not by claim: does the repo carry the router on its default branch? Do the dispatch files carry the header? A small self-silencing daily check (opens one tracking issue while pending; closes itself on landing) beats asking people if they've adopted.

## The standard: same-day propagation

The mature loop doesn't wait for the weekly pass. Real week, anonymized: one lane's worker hung at an interactive prompt → that lane wrote the dispatch-header rule → the lead codified it into shared memory and the boot protocol → every lane inherited it the same day. Over one week a single lane under deadline pressure contributed four permanent guards this way. The weekly retro then *harvests and audits* the loop rather than being its only turn.

## Proof it works on itself

The strongest validation of a process layer is that its own tools catch its own bugs. Ours did, three times in one week: an adversarial review wrapper's bugs were found *by teams dogfooding the wrapper* (a pipe-hang class, twice; a large-diff hang), each fix landing in the shared tool within hours, each converted to a standing rule. If your process tooling has never caught a bug in your process tooling, it isn't being used adversarially enough.

## Anti-patterns

- **Retro theater** — long retros, no ingest pass, nothing ever becomes a guard.
- **Silent fixes** — a lane solves its friction locally and the fix never propagates; every other lane rediscovers it the hard way.
- **Claimed adoption** — "we're using it" with no artifact check behind it.
- **Interrupting to improve** — process improvements are fed to a lane's queue, never injected mid-flight into a worker already executing (finish-what-you-started applies to process changes too).
