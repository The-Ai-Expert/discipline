---
name: theaiexpert-suggest-adversarial-review
enabled: true
event: bash
pattern: gh\s+pr\s+create
action: warn
---

**[theaiexpert-suggest-adversarial-review]**

**Opening a PR.** Did Codex (or a peer) review the diff before this?

The discipline is to pair every PR with an adversarial review pass before merge — not after. Two paths depending on task class:

- **Tactical code PR (no UX, internal logic):** run `/adversarial-review` from this terminal. Captures the diff, runs Codex review, auto-applies critical fixes, loops to APPROVED. ~1 command.
- **Substantive / UX-visible PR:** spawn your own Codex session (tmux or equivalent), send the PR + dispatch as context, review by hand. Preserves human gates on visible changes.

If you've already run review, ignore this. If you haven't — do it before merging, not after. Decision matrix lives in `skills/codex-review-hybrid-policy.md`.

**Why:** Principle #5 (verify behavior, not proxy). CI green + tests green is one safety net; an independent adversarial pass on the *diff itself* is a different and complementary safety net. The combination catches what either alone misses.

**Prereq:** the `adversarial-review` plugin from `claude-community` must be installed for `/adversarial-review` and `/adversarial-plan` to be available. `claude plugin install adversarial-review` if missing.
