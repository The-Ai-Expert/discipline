---
name: evidence-chain
description: Evidence before conclusion. Never escalate a plausible-but-unverified finding; verify inputs, check deterministic gates, and read the actual trace before blaming the most dramatic candidate. Beware the seductive-confirmation trap. Use when diagnosing a failure, a root cause, or any alarming result - especially one that confirms a fear you already held.
---

# The evidence chain

The most dangerous finding is the one that confirms what you already suspected. It feels like vindication, so you escalate it, and it turns out the fear was real but the finding was wrong. This skill is the discipline that sits between "I found something alarming" and "I told a human it was broken".

The rule: **evidence before conclusion.** Walk the full chain before you name a root cause.

---

## Never escalate a plausible-but-unverified finding

Plausible is not verified. A finding can be entirely consistent with your mental model and still be false. Before a finding leaves your hands and reaches a human as "X is broken", it has to survive the chain below.

Before diagnosing a root cause:

1. **Verify the inputs were complete.** Was the data the system saw actually the data you think it saw? A pipeline that "produced the wrong answer" often received the wrong inputs - a truncated upload, a silently-dropped record, a partial batch. Check the input before blaming the processing.
2. **Check the deterministic gates and filters.** Before you conclude the intelligent part misbehaved, rule out the dumb parts. A too-strict filter, an over-eager validation gate, a threshold set wrong - these are deterministic and cheap to check, and they cause symptoms that look exactly like a deeper failure.
3. **Read the actual trace.** Not the summary of the trace. Not what the trace probably says. The trace. The line that actually threw, the value that was actually present, the branch that was actually taken.

Only after all three survive do you get to blame the dramatic candidate. Usually one of the three has already explained it.

---

## The seductive-confirmation trap

A scary result that matches a hypothesis you already held feels confirmed. That is precisely the moment to verify hardest, not the moment to relax.

The mechanism is simple and it catches everyone: you had a worry, a result arrived that matches the worry, and matching a prior feels like proof. It is not proof. It is a coincidence until the chain is walked, and the more the result flatters your existing theory the more scrutiny it has earned, not less.

Concretely, two findings should trip the alarm on themselves before they trip an alarm to a human:

- **"The work doesn't exist."** Before concluding a piece of work was never done, test the possibility that the *checker* is looking in the wrong place - a stale checkout, an unfetched branch, the wrong environment, a reader without the permission to see it.
- **"The system is broken."** Before concluding the system is down, test the possibility that the *checker* is broken - wrong credentials, wrong region, wrong endpoint, a principal that lacks the visibility rather than a system that lacks the data.

When a finding indicts something, ask whether it might actually be indicting your instrument. A broken thermometer reads "fever" too.

---

## A worked example

A CI check reported that a critical database column was missing. The column being absent would be a genuine emergency, and the check said so in alarming terms. The seductive move is to escalate immediately: the schema is broken, production is at risk, wake someone up.

The evidence chain said otherwise. Walking it:

- The application was working. Every transaction that wrote to that column was succeeding. A missing column would have failed those writes loudly.
- The column had been added by a migration, and that migration was recorded as applied.
- The value was being written on every relevant operation and read back correctly by the running app.

So the column plainly existed. The only thing consistent with all of that evidence *and* with the check's complaint was that the checker itself could not see the column - its database principal lacked the metadata visibility to read the schema catalogue, even though the data and the column were both fine.

The instrument was blind, not the system broken. The alarm was retracted before it reached a human. Had it been escalated on the strength of "plausible and scary", it would have burned someone's time chasing a problem that did not exist, and eroded trust in the next alarm that was real.

---

## The habit

For any alarming finding, before it leaves your hands:

- State the finding as a hypothesis, not a fact.
- Name what would have to be true for it to be real, and check each of those.
- Explicitly ask: could the checker be wrong instead of the checked? Test that branch with the same rigour you would test the system.
- Only escalate what survives. Escalate the retraction just as clearly as you would have escalated the alarm.

A retracted false alarm costs you a few minutes. An escalated false alarm costs a human their afternoon and costs your next real alarm its credibility.

---

## Tied principles

- **#1 Think before coding** - verify, do not recall; a hypothesis that matches a question you already had is the most seductive whim, and the one to distrust most.
- **#5 Verify the behaviour, not the proxy** - a checker's output is a proxy for the system's state; when the two disagree, one of them is lying and it is often the checker.
