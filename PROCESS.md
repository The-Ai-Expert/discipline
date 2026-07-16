# How we ship with agents — The Ai Expert process

*The operating model behind our AI-assisted delivery: one human tech lead, a small fleet of orchestrator agents, and the discipline that keeps a fleet honest. Everything here is packaged from real production delivery — every rule exists because its absence cost us something specific.*

---

## The shape of the team

Three roles (detailed in the `layered-orchestration` skill):

- **The human tech lead** owns direction, UX judgment, irreversible actions, and anything a wrong call costs real money or trust. Everything else is delegated.
- **Per-project orchestrators** — one agent session per delivery lane. Each owns its lane end-to-end: brainstorms and specs with the lead, dispatches background workers, runs review gates, merges, verifies. **Lanes never cross**: an orchestrator touches only its own repo and schema.
- **A lead orchestrator / PM** above the lanes. It owns the shared layer (process, tooling, cross-project truth), maintains the work board, and **feeds priorities into the lanes — it never dispatches work itself**. One boss per lane.

Coordination between concurrent *orchestrator* sessions happens on a **shared board file** — the only orchestrator-to-orchestrator channel. Claim shared resources before use, heartbeat while holding them, write section-scoped appends so sessions never clobber each other. (Workers have their own upward channel — the BLOCKED-file + committed-escalation path below — which their owning orchestrator actively watches; the board and the worker channel are both monitored, neither substitutes for the other.)

## The six principles

Every change, every dispatch, every review honors these (full text in the `six-principles` skill):

1. **Think before coding** — surface ambiguity; never silently pick an interpretation.
2. **Simplicity first** — nothing beyond what was asked; no speculative flexibility.
3. **Surgical changes** — every changed line traces to the request.
4. **Goal-driven execution** — failing test first; if you can't test it, the contract is unclear.
5. **Verify the behaviour, not the proxy** — green tests and clean builds are proxies; the standard is the user-facing outcome observed in the real environment. Anything shipped proxy-verified is tracked as **verify-debt** until behaviour-verified (`verify-debt-ledger` skill).
6. **Finish what you started** — a dispatched worker owns its scope until done or blocked. New scope gets a new worker, never a mid-flight interruption. This applies to process changes too.

## The delivery loop

```
Spec → Plan → Tickets → Branch → Failing test → Implement → Simplify
     → Run the app → Adversarial review gate → PR → Merge → Verify live → Update tracker
```

Ceremony scales with the change — a typo fix doesn't get the full loop; a behaviour change always does. Two structural rules:

- **Reviews are adversarial and cross-model.** The reviewing agent is a different model than the author (we pair Claude-authored work with a Codex-driven gate and vice versa; policy in `codex-review-hybrid-policy`). Review findings are authoritative: FAILs are fixed before merge, and verdicts are posted on the PR, not lost in a terminal.
- **Every repo carries an `AGENTS.md` router** so *any* agent — any vendor — knows how the repo works: commands, gates, gotchas. After editing the router, review it against the actual tree; an aspirational router that names files that don't exist is worse than none (`agents-md-router` skill).

## The review doctrine: deterministic-first, LLM-last

Cheap deterministic gates (linters, tests, denylists, structural checks) review everything; the LLM reviews only the subjective residual. **Every recurring catch becomes a permanent deterministic gate** — the flywheel that makes the cheap layer grow and the expensive tail shrink. And when a gate fires mysteriously: **measure the guard, don't reason from code** — persist the actual rejection before theorizing. (Full doctrine + the cautionary tales in `deterministic-first`.)

## Workers: non-interactive by construction

Background workers have nobody at the keyboard, so a single permission prompt hangs them forever — and the harness reports them as "working" throughout. Three layers, all mandatory (`background-workers` skill):

1. **Enforce** — interactive-prone tools are disallowed at spawn, not merely discouraged in prose.
2. **Detect** — a watchdog greps worker logs for the interactive tell; status metadata is *not* a liveness signal — commit cadence is.
3. **Self-alert** — a blocked worker writes a BLOCKED file with its question and stops; it never sits on a prompt.

Plus: every dispatch opens with a canonical env-rule header (the rule lives in the dispatch text, not in memory a worker won't read), and parallel mutating workers get separate git worktrees — different files do **not** protect a shared git index.

## Truth-keeping: derive, don't trust

Work state is **derived from ground truth** — merged-PR state, code consumption, deploy evidence — never from labels or self-reports (`wip-registry` skill). The registry's signature catch is **abandoned-at-N%**: merged but never wired, "Done" on the ticket, dead in the product. It corrects its own false alarms the same way — by checking the artifact. Before any net-new design: the **grounding gate** — does this already exist, did we start it, how done is it?

## The self-improving loop

Sessions emit short friction retros; a weekly pass distils *recurring* items into permanent fixes; adoption is tracked by artifact, not by claim (`retro-flywheel` skill). The mature loop propagates same-day: one lane's incident becomes every lane's standard within hours. The proof standard: **your process tooling should be catching bugs in your process tooling.** Ours does.

## What the human actually does all day

With the fleet running, the tech lead's day is: set direction, make the calls only a human can make (UX, irreversibility, money), answer BLOCKED files, read verify-debt and stalled lanes off the board, and say "go" — while the orchestrators handle dispatch, review, merge, verification, and the truth-keeping that makes any of it trustworthy.

---

*The skills, hooks, and commands in this repo are the executable form of this document. Start with `onboarding`, install the hooks, and steal anything useful — MIT.*
