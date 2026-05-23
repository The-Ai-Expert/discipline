---
name: theaiexpert-require-live-validation
enabled: true
event: stop
pattern: .*
action: warn
---

**[require-live-validation]**

Before claiming work is complete:

1. **Reproduced in the real environment?** Did you load the page / hit the endpoint / exercise the feature in the same surface the user uses?
2. **Live evidence captured?** Screenshot / API response / log line — not just "tests passed."
3. **Deploy actually shipped?** Check the CI run and the live artefact (bundle hash, function listing, etc.) — both green.

**Why:** Principle #5 (verify behavior not proxy). Tests + tsc + lint + CI all pass; the live app is broken — happens routinely. The proxy-vs-behavior gap closes only at the live verification step.

If the gap can't be closed in your session (e.g., production rollout needs a window), say so explicitly in your report under "Unverified — reviewer please decide." Don't ship "Done" with the gap unsurfaced.
