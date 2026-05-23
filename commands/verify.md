---
description: Walk a structured live-verification of a recently-shipped change. Distinguishes proxies (tests, CI, lint) from real user-facing behavior (Principle #5).
---

# /verify — Structured live verification

Run this after a worker / agent reports "done." Walks the proxy-vs-behavior gap explicitly and surfaces the evidence.

## The checklist

1. **CI status:** `gh run list -L 3 --json status,conclusion,displayTitle` — confirm latest run is green for the target branch
2. **Bundle / asset hash (for SPAs):** curl the deployed entry point, confirm new hash vs pre-merge (use cache-busting query param)
3. **DOM presence (for FE changes):** open the deployed page, query for the specific test-id or selector the change added; confirm visible
4. **API behavior (for BE changes):** hit the affected endpoint with realistic payload; confirm response shape + status code + side-effect (DB row written, file uploaded, message sent)
5. **Functional spot-check:** exercise the change as the actual user would — click, type, submit, scroll — and confirm the user-facing outcome
6. **Side-effect check:** anything the change *shouldn't* affect — still works? (Pre-existing tests didn't break; adjacent pages still load; logs not flooded)

## How to report

After running these checks, write the result under a `Verified` section in your `.reports/*.md` artefact. If you skipped a check, write it under `Unverified — reviewer please decide` with residual-risk classification.

```markdown
## Verified

- CI run [run-id] passed
- Bundle hash flipped: `index-AAA.js` → `index-BBB.js`
- DOM check: `[data-testid="..."].length === N` on `/route`
- API row written: `[table]` row `[id]` carries `[fields]`
- Functional spot-check: [user action] → [observed outcome]

## Unverified — reviewer please decide

- [check skipped]
- Residual risk: HIGH | MED | LOW
- If the gap hides a bug, symptom would be: [one sentence]
```

## Tied principle

Principle #5 — Verify behavior, not proxy. CI green is a proxy. A DB row written by a real user action is behavior. The proxy-vs-behavior distinction is the most-violated discipline in AI-assisted engineering; this command names it explicitly.

## When this command saves you

The failure mode it catches: worker reports "done" with all proxies green, but the change isn't actually mounted / wired / deployed correctly. The user-facing outcome is silently absent. Hooks fire later in production. `/verify` catches it before the user does.

A real example pattern (from real-world delivery):

> A worker shipped a UI component upgrade that added a required user-input field. All tests passed. CI green. PR merged. Deploy succeeded. Bundle hash flipped. The component was a feature-complete masterpiece. **It was not mounted on any page.** No user ever saw it. Discovered only on a `/verify` walk.

Run /verify after every "done" claim.
