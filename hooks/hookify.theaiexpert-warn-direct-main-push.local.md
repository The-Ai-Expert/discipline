---
name: theaiexpert-warn-direct-main-push
enabled: true
event: bash
pattern: git\s+push.*main|git\s+push\s+origin\s*$
action: warn
---

**[warn-direct-main-push]**

**Pushing directly to main.** Check which workflow applies:

**Direct push to main is OK for:**
- Bug fixes under ~50 lines / single file / no API or schema changes
- Build fixes
- Batching small fixes in one commit

**Use a PR (with adversarial review) for:**
- Features
- Multi-file changes
- API or schema changes
- Anything that warrants a second pair of eyes
- Anything load-bearing

When in doubt, PR.
