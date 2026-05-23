---
name: Test feedback (early adopter)
about: Structured report from running through the smoke checklist
title: "[test] "
labels: ["test-feedback"]
assignees: []
---

## Environment

- OS:
- Claude Code version (`claude --version`):
- Did `hookify` plugin install cleanly?  Yes / No / Already installed
- Did `claude plugin install The-Ai-Expert/discipline` (or `git clone`) work?  Yes / No
- Did `/install-hooks` complete and copy 10 files?  Yes / No

## Smoke test results

Walking the checklist in the README:

- [ ] Smoke test 1 — Hook discovery (`ls ~/.claude/hookify.theaiexpert-*.local.md | wc -l` = 10)
- [ ] Smoke test 2 — `theaiexpert-enforce-dispatch-discipline` hook fires on dispatch file write
- [ ] Smoke test 3 — `/dispatch` slash command resolves + renders template
- [ ] Smoke test 4 — Six Principles skill recites with mechanics
- [ ] Smoke test 5 — `theaiexpert-require-unverified-section` hook fires on `.reports/*.md` without Verified section

If any failed, paste the unexpected output here:

```
<paste output>
```

## What was confusing

(Anything in the install / first 10 min that needed a second read or external context to understand?)

## Hook noise

(Did any rule fire too often, too rarely, or with a message that wasn't actionable?)

## What works well

(So we don't regress it.)

## Anything broken / surprising

(Plain-language description of the unexpected behavior. The more specific, the more we can fix it.)

## Suggestion for the README / docs

(What sentence or section would have saved you the most time?)
