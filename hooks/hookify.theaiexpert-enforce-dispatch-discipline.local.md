---
name: theaiexpert-enforce-dispatch-discipline
enabled: true
event: file
action: warn
tool_matcher: Edit|Write|MultiEdit
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.reports/dispatches/dispatch_.*\.md$
---

**[enforce-dispatch-discipline]**

🛑 **Dispatch written / edited.** Before firing the worker (`claude --bg` or equivalent), walk this checklist:

```
☐ I read EVERY file path referenced in this dispatch in THIS session
  (not from memory — `cat` / `Read` / `git grep` each one)

☐ I verified factual claims about codebase state
  (enums, allowlists, hook names, route paths, column names — actual file reads)

☐ Acceptance criteria are specific + testable + traceable to the originating ask
  (not "looks good"; not "ships cleanly")

☐ Length is under ~150 lines, OR I've named the reason it's longer

☐ If this dispatch encodes a BLOCKER, spec, or load-bearing pattern,
  I ran adversarial review on the dispatch text before spawning
```

**Why:** Orchestrator-authored dispatches with factual errors propagate downstream. The cleanest place to catch these is at dispatch-write time, not at worker-execution time. This hook fires at exactly that boundary.

**Tied principles:**

- Principle #1 — *Think before coding. State assumptions. Never guess.* Verifying factual claims by reading the actual files satisfies this directly.
- Principle #2 — *Simplicity first.* Length check enforces the dispatch-side equivalent of "minimum code that solves the problem."
- Principle #4 — *Goal-driven execution.* "Acceptance criteria specific + testable + traceable" is the dispatch-side version of "verifiable success criteria before writing a line."

**If you can't honor a box, name the deviation in the dispatch itself before firing the worker.** Don't silently dispatch with unchecked boxes — that's the failure mode.
