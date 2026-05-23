---
name: check-deploy-after-merge
enabled: true
event: bash
pattern: gh\s+pr\s+merge
action: warn
---

**[check-deploy-after-merge]**

**PR merged.** Now verify the deploy actually shipped:

1. **CI status:** `gh run list -L 1 --json status,conclusion,displayTitle` — confirm green
2. **Bundle/asset hash flip (for SPAs):** curl the deployed entry point, confirm a new bundle/asset hash vs pre-merge
3. **Functional spot-check:** hard refresh in the browser and exercise the fix — don't trust the deploy log alone
4. **Service restart (when needed):** if the project requires it (e.g., Azure Functions, some Node servers), restart the service explicitly

**Why:** Principle #5 (verify behavior not proxy). "CI green + merged" is a proxy for "shipped + working." Real verification is the bundle hash flipping AND the change being visible in the live app. Many silent-deploy failures hide between these two signals.

Don't mark the work complete until you've reproduced the user-facing behavior in the live environment.
