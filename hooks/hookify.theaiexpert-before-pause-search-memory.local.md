---
name: theaiexpert-before-pause-search-memory
enabled: true
event: all
action: warn
tool_matcher: AskUserQuestion
---

**[theaiexpert-before-pause-search-memory]**

🛑 **You're about to pause and ask the user. STOP — search your own memory/notes FIRST.**

Before this question goes to the human, run the precedent check:

```
☐ Scanned your persistent memory / notes index for a related decision, rule, or reference
☐ Searched for the specific thing you're stuck on (an infra path, a credential location,
  a service ID, a prior fork the user already ruled on, a documented pattern)
☐ Checked your current working-context / task file for an in-flight resolution
☐ Asked yourself: is this a DIRECTED fix or a known lookup? → solve it, don't ask
```

- **Found a precedent?** Apply it and CANCEL this question.
- **Genuinely no precedent AND it's a real product/design fork, an irreversible/outward-facing
  action, or a true blocker?** Then ask — and say so: note that you searched and found none.

You are the engineer in the loop. Reserve the human for decisions only they can make — not
status, not directed work, not configuration that's already written down. Pausing and re-asking
what's already in your memory erodes trust and wastes the user's time.

**Principle ties:** #1 (think before acting; don't guess — but also don't outsource what you can
resolve) and the autonomy calibration baseline (act on the reversible/known; escalate only the
genuine fork).
