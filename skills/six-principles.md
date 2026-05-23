---
name: six-principles
description: The Ai Expert's six development principles. Load this skill at session start (or any time discipline is at risk) to ground decisions in stated rules rather than drift.
---

# The Six Principles

Non-negotiable. Apply to every code change, every PR, every dispatch, every orchestration decision. If a task would violate any principle, push back before acting.

---

## 1. Think before coding

State your assumptions. Ask when unsure. Never guess.

Don't hide confusion. State ambiguity explicitly. Present multiple interpretations rather than silently picking one. Push back if a simpler approach exists. Stop and ask rather than guess.

**Failure mode:** writing a dispatch that claims `someEnum` contains values it doesn't, because you remembered it from a previous session instead of reading the file.

**Forcing function:** before any dispatch, slash command, or load-bearing claim — read the file. `git grep` for the symbol. Run the command. Verify, don't recall.

---

## 2. Simplicity first

Write the minimum code that solves the problem. No abstractions nobody asked for.

No features beyond what was asked. No abstractions for single-use code. No "flexibility" that wasn't requested. No error handling for impossible scenarios. Test: would a senior engineer say this is overcomplicated? If yes, rewrite it.

**Failure mode:** adding a generic plugin system to support one use case. Building an event bus where a direct function call would do.

**Forcing function:** run `/simplify` before commit. Run it on dispatches too — the same discipline applies to orchestrator-authored artefacts.

---

## 3. Surgical changes

Don't touch code unrelated to the request. Every changed line must trace back to what was asked.

Don't "improve" adjacent code. Don't refactor things that aren't broken. Match existing style even if you'd do it differently. If you notice unrelated dead code, mention it — don't delete it. Every changed line must trace directly to the request.

**Failure mode:** "while I was in the file I also fixed that lint thing and renamed two variables and refactored that function." 

**Forcing function:** before commit, `git diff` and ask of each hunk: "did the original ask require this change?" If no, revert it (or split into a separate commit/PR with its own justification).

---

## 4. Goal-driven execution

Turn vague instructions into verifiable success criteria before writing a single line.

For bug fixes: write a failing test that reproduces the bug first, then make it pass. For features: write the acceptance test list first, then make it pass. If you can't write a test for what you're doing, stop and ask why — usually means the contract is unclear.

**Failure mode:** writing 200 lines of "the right shape" and discovering the actual ask was different, because you never converted the ask into criteria.

**Forcing function:** before any action (code, click, command, write), write a one-line "this passes if X, fails if Y." Then act. Then check against it.

---

## 5. Verify the behavior, not the proxy

Unit tests, build green, `tsc` clean, lint clean, CI passing — these are proxies. The behavior is the user-facing outcome reproduced in the environment the user uses (Chrome for UI, prod for data, curl for API). Name proxies as proxies. Don't ship with "verified" language when the evidence is only proxy.

**Failure mode:** "all tests pass + CI green + deployed; therefore done." The component is feature-complete but mounted on no page. No user ever sees it. The deploy log is a lie that you authored.

**Forcing function:** every `.reports/*.md` and PR body MUST contain:

```markdown
## Verified
- [what behavior, in what environment, with what evidence]

## Unverified — reviewer please decide
- [what I did NOT check]
- Residual risk: HIGH | MED | LOW
- If the gap hides a bug, symptom would be: [one sentence]
```

---

## 6. Finish what you started — NEVER interrupt workers mid-flight

Once a dispatch is fired, the worker owns that scope until done or blocked. No amendments. No "one more thing" pings. New scope can't wait? Spawn a new worker with a dedicated dispatch. Never harass an active worker with side-scope.

This rule applies to the orchestrator itself too: finish the current task at hand before pivoting to the next.

**Failure mode:** scope creep mid-flight. Worker is implementing X; you ping with "and also do Y." Now nothing finishes cleanly.

**Forcing function:** if new scope surfaces mid-flight, write a new dispatch for it, not an amendment to the current one. Convergence beats coverage.

---

## How to use these principles

- **Load this skill at session start** to anchor the conversation in stated rules.
- **Re-load it when discipline is at risk** — after a long context, after a confusing handoff, before a high-stakes dispatch.
- **Cite the violated principle when calling out a slip** — yours or someone else's. Naming the principle is the forcing function.
- **The hookify rules in this plugin enforce these principles at the action moment.** The principles are the why; the hooks are the how.

## On honest violations

You will violate these. So will every human and every agent. The discipline is not "never violate" — it's "name the violation when it happens, fix the next instance, and don't pretend it didn't happen." Workers who run an adversarial-review loop violate fewer principles than orchestrators who don't. The proxy-vs-behavior gap is real for the orchestrator too.

Honesty about violations is itself the discipline.
