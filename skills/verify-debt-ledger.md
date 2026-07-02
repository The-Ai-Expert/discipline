---
name: verify-debt-ledger
description: Kill the silent "waiting to verify" queue. Track everything shipped proxy-verified but not yet behaviour-verified as an explicit, visible ledger item; batch the live-verification pass; build no-human verification paths for recurring gates. Use when work ships behind a live gate, or when "done" is at risk of meaning "tests passed".
---

# The verify-debt ledger

Principle #5 says: verify the behaviour, not the proxy. But real delivery has a gap between the two. Tests are green now; the live check has to wait for a deploy, an authenticated session, a data backfill, a stakeholder with the right permissions. That waiting period is where "done" quietly rots into "we think it's done".

The fix is to name the gap and track it. Anything shipped proxy-verified but not behaviour-verified is **verify-debt** - a tracked, visible line item, never a silent "done".

---

## What counts as verify-debt

A change is in verify-debt the moment all of the following are true:

- Its proxies are green - tests pass, type-check clean, build succeeds, CI passing.
- Its behaviour has **not** been reproduced in the real environment the user uses (Chrome for UI, prod data for a query, curl for an API, the authenticated app for a permissioned flow).
- It has nonetheless been merged, deployed, or reported as shipped.

That last point is the trap. Green proxies plus a merge feels like completion. It is not. It is a debt you have taken on, and like any debt it is fine as long as it is on the books and being serviced. The failure is the debt that is invisible.

---

## The ledger

Keep an explicit ledger of open verify-debt. Each item records three things:

| Field | What it captures |
|---|---|
| **What** | The behaviour that still needs live verification, in one line. Not "the PR"; the user-facing effect the PR was supposed to produce. |
| **Gated on** | Why it can't be verified yet - the deploy, the authenticated session, the permission grant, the data that has to land first. |
| **Since** | When it entered the ledger. Age is the signal. A three-week-old verify-debt item is a red flag regardless of how confident the proxies were. |

An item leaves the ledger exactly two ways: its behaviour is verified in the real environment, or it is **explicitly waived** with a named reason and an accepted residual risk. It never leaves the ledger by being forgotten.

---

## Monitoring stays armed

The corollary: monitoring on a shipped-but-unverified change stays armed until the ledger item closes. "I'll notice if it breaks" is not a plan. The item is either being actively watched or it is behaviour-verified. There is no third state where you have stopped watching but also never confirmed it works.

This is the discipline that prevents the classic post-deploy silence: everyone assumes someone else confirmed the live behaviour, nobody did, and the first confirmation arrives as a customer report.

---

## Surface it as a batched queue, never a dribble

Verify-debt is surfaced to the human as a **batch**, not a stream of one-off asks.

The wrong pattern: pinging the human every time a single item becomes verifiable - "can you check this one thing", then an hour later "can you check this other thing". That trains the human to ignore you.

The right pattern: accumulate the debt, then run one authenticated-session pass through many items at once. One login, one context-load, many confirmations. The human's attention is the scarce resource; spend it in a single well-prepared sweep, not in a hundred interruptions.

And never the third pattern: leaving it silent. A batched ask is far better than no ask, and no ask is the failure mode this whole skill exists to prevent.

---

## The systemic goal: no-human verification paths

Batching is a mitigation. The real win is eliminating the human from the loop entirely for each recurring gate.

For every gate that keeps generating verify-debt, ask: can the check itself assert the actual effect instead of a proxy for it? Examples of the move:

- A test that currently asserts "the migration step ran" is upgraded to assert "the column now exists and holds the expected value".
- A deploy check that currently asserts "CI went green" is upgraded to fetch the deployed bundle and confirm the new code is actually live.
- A permission flow that currently needs a human to log in and click is upgraded to a scripted authenticated request that asserts the response.

Each such upgrade retires a recurring line of verify-debt permanently. The ledger tells you which gates recur; those are the ones worth the investment of a no-human path.

---

## Tied principles

- **#5 Verify the behaviour, not the proxy** - verify-debt is the explicit accounting of every place a proxy has stood in for behaviour. The ledger is Principle #5 made into a running total.
- **#4 Goal-driven execution** - an item's "what" is its success criterion. If you cannot state what verifying it would prove, the criterion was never clear.
- **#2 Simplicity first** - do not build a heavyweight tracking system on day one. The ledger can be a section in a status file. Invest in the no-human verification paths where the debt recurs, not everywhere.
