---
name: deterministic-first
description: The core review doctrine - cheap deterministic gates check everything, the LLM reviews only the subjective residual, and every recurring catch becomes a permanent deterministic guard (the flywheel). Includes "measure the guard, don't reason from code". Use when designing any review/QA pipeline, when an LLM reviewer keeps re-finding the same defect class, or when diagnosing why a guard fired.
---

# Deterministic-first, LLM-last

LLM review is the most expensive, least repeatable check you own. Spend it only where judgment is genuinely required, and convert everything else into gates that run in milliseconds and never get tired.

## The doctrine

1. **Deterministic gates review everything.** Linters, tests, schema checks, denylists, structural rules — cheap, fast, byte-exact, run on every change.
2. **The LLM reviews the residual.** Semantic questions, novel defects, "does this read right" — the subjective tail the gates can't express.
3. **The flywheel:** every time the LLM (or a human) catches a defect that is **recurring or generalizable**, add a permanent deterministic guard for that class. The cheap layer grows; the expensive tail shrinks. A one-off subjective finding does not earn a lint rule — reserve guards for *classes* of defect.

Real example of the flywheel in one afternoon: an LLM review caught a brand term leaking into a rendered page that a hand-rolled grep had missed. The fix wasn't "run the LLM more often" — it was a denylist check compiled into the build that now *fails the build* if any governed term would render. The LLM's catch became infrastructure.

## Measure the guard, don't reason from code

When a gate rejects something and you don't know why, the failure mode is to *read the gate's source and theorize*. Theories written from code are convincing and frequently wrong.

The discipline: **make the gate tell you.** Persist the actual rejected artifact and the actual violation object. Then fix the real cause once.

Cautionary tale (real, anonymized): generated ad copy failed a compliance gate 0-for-4. Two PRs shipped based on a plausible code-reading — banning a phrase category the gate "must have" objected to. Both PRs solved a non-problem: when instrumentation finally persisted the real rejection, the cause was **copy overflow in the smallest ad slot** — a length rule, not a language rule. One measured fix (make generation fit-aware to the tightest slot) closed it. The two speculative PRs were reverted.

Rule of thumb: if you're about to fix a guard-rejection and you cannot paste the guard's actual output into the PR description, you're guessing. Instrument first.

## Where the LLM tail earns its keep

- Semantic leaks a fixed list can't express ("this phrasing implies a claim we can't make")
- Novel defect classes — the first instance, before the flywheel turns it into a gate
- Adversarial review of *reasoning* (specs, plans, architecture) where there is no artifact to gate yet
- Judging whether a deterministic gate's rule is still the *right* rule

## Anti-patterns

- **LLM as linter** — asking a model to check things a regex can check, on every diff, forever.
- **Silent gate-tuning** — loosening a deterministic rule so the pipeline goes green, without recording why.
- **Guard archaeology** — three engineers reasoning about why a gate fired when a one-line instrumentation patch would answer it.
- **Flywheel skipped** — the same LLM finding appearing in three consecutive reviews. That's not review, that's a missing gate.
