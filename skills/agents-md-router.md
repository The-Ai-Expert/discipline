---
name: agents-md-router
description: The AGENTS.md router pattern - one repo-root file that tells ANY coding agent how this repo works (run/test/deploy, review gates, gotchas), with a greppable 7-line header and a self-review rule that keeps it honest against the actual tree. Use when standing up a repo for agent work, when Codex/Cursor agents ignore your CLAUDE.md, or after editing a router.
---

# The AGENTS.md router

Claude Code reads `CLAUDE.md`. Codex, Cursor, and most other coding agents read **`AGENTS.md`** — and ignore `CLAUDE.md` entirely. If your discipline lives only in `CLAUDE.md`, half your reviewers are flying blind. The router is one repo-root `AGENTS.md` that becomes the single source of truth for every vendor: Codex/Cursor-class agents load it natively, and your `CLAUDE.md` delegates to it with one line ("Read `AGENTS.md` first — it is the repo router") so Claude Code lands on the same content rather than a divergent copy.

## Shape

- **First ~7 lines are a greppable summary**: project one-liner, stack, run/test/lint/deploy commands, the golden rule, what to ask before touching. An agent (or human) greps this before reading anything in full.
- **The loop**: how work happens here — spec → failing test → implement → simplify → run the app → review gate → PR. State explicitly that ceremony matches the change (a typo fix does not get the full loop) and that PRs/outward actions need confirmation.
- **Tools**: the shared helper scripts an agent should reach for instead of re-deriving incantations, and where project-specific helpers live.
- **Review policy**: deterministic-first, LLM-last (see the `deterministic-first` skill); who reviews, with which model, at plan and implementation.
- **The per-repo body** — the part only the repo owner knows: exact commands, test inventory, conventions, and the gotchas that bite (auth quirks, migration ordering, env-var traps).

## The self-review rule (load-bearing)

**After editing the router, run your adversarial review tool against it.** A review agent verifies the router's *claims against the actual tree* — every command exists, every named file is on this branch, every "the behaviour is verified by X" is true.

Why this is not optional: an **aspirational router is a live footgun**. The first team member to adopt this pattern wrote their router describing the world *after* a not-yet-merged PR — it named three scripts that didn't exist on the branch. An agent following that router hits "no such file" at the mandatory verification gate and either stalls or improvises. The same self-review also caught a stale deploy doc (wrong region, claimed unprovisioned while production was live) and a proxy-verification claim ("CI green is the behaviour" — false, CI omitted the browser suite). A router full of dead paths is worse than no router.

## Keeping it honest over time

- The router is code-adjacent documentation: it changes in the same PR as the behaviour it describes.
- If your fleet has an adoption checker (a daily job that verifies `AGENTS.md` exists on the default branch), remember its criterion: **on the default branch**. A beautiful router on an unmerged feature branch is adoption-in-substance, not adoption.
- Docs referenced by the router must exist on the branch that carries the router. A spec cited in a PR body but never committed is a dangling reference and — for anything compliance-adjacent — a defensibility gap.
