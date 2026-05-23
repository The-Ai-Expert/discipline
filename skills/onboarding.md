---
name: onboarding
description: New user / team-member onboarding. Walks the discipline narrative + tooling setup + first dispatch experience. Use when someone installs the plugin for the first time, or asks "how do I use this?"
---

# Welcome to The Ai Expert discipline

You've just installed the `theaiexpert-discipline` plugin. This skill orients you to what you've installed and how to use it.

## What this plugin gives you

Three layers:

| Layer | Artefact in this plugin | What fires when |
|---|---|---|
| **Convention** (what to know) | `skills/six-principles.md` — the six development principles | Loaded at session start (or by `/principles` invocation) |
| **Tooling** (what fires at the action moment) | `hooks/*` — 10 hookify rules<br>`commands/dispatch.md` — `/dispatch` scaffold<br>`commands/verify.md` — `/verify` walk | At write / commit / push / dispatch / verify events |
| **Specialised review** | `agents/*` — domain SME agents | When invoked by name |

## First 10 minutes

1. **Read the Six Principles** (`skills/six-principles.md`). They're short. They drive everything else.
2. **Watch a hook fire.** Try `git commit` in any project — the `require-simplify-before-commit` hook will warn you. Hooks are advisory; you can proceed past them; the point is they make the discipline visible at the action moment.
3. **Try `/dispatch`** when you next have work to hand to a background agent or teammate. The scaffold forces you to think through "what does done look like" before you start.

## First real workflow

A typical loop on a non-trivial feature:

```
1. Brainstorm with stakeholder → goal-driven success criteria written down
2. /dispatch → produces a compliant dispatch file at .reports/dispatches/dispatch_*.md
3. Hand to a worker (background agent / contractor / teammate) — or do it yourself
4. Worker writes failing test first (Principle #4)
5. Worker implements (Principle #2 — simplicity)
6. Worker runs /simplify (hook reminds them at commit)
7. Worker pushes (hook reminds them to verify build)
8. Worker opens PR (hook reminds them PR body needs Verified/Unverified sections)
9. Adversarial review (Codex or peer)
10. Merge (hook reminds you to check deploy)
11. /verify (live behavior verification, not just proxy)
12. Report at .reports/*-YYYY-MM-DD.md (hook enforces Verified/Unverified shape)
```

Each step has discipline encoded; you don't have to remember the rules — the hooks remind you.

## What this plugin does NOT give you

- Infrastructure setup (Claude Code, Codex CLI, GitHub, Linear, Slack — those install separately)
- Project-specific CLAUDE.md files (those live in each project repo)
- Customer / domain-specific SMEs (those stay project-local — this plugin only ships team-shareable generic SMEs)
- A theory of how to brainstorm or scope work (the discipline is for *executing* clean once scope is set)

## When the discipline feels heavy

The principles cost a small amount of effort at every action moment and save a large amount of effort at every "wait, that's not what was supposed to ship" moment. The breakeven is somewhere around the third re-work loop.

If a hook feels like it's nagging — read what it's saying. The hook is probably right. If it's wrong (false-positive), file an issue against the plugin so the rule gets tightened.

## How to extend / customise

- Add project-specific hookify rules locally in `~/.claude/hookify.*.local.md` — they coexist with plugin hooks
- Override a plugin hook by disabling it in `~/.claude/hookify.[name].local.md` with `enabled: false`
- Submit a PR upstream to this plugin if your improvement generalises

## Who built this

The Ai Expert (Erik Schwartz, et al.). Packaged from real-world delivery experience. Open source under MIT — fork, adapt, contribute back if your discipline iteration is good.

## Next

Run `/six-principles` to load the principles into your active context. Then go ship something.
