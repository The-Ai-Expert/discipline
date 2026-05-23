# The Ai Expert Discipline

**A Claude Code plugin that encodes production-grade orchestration discipline at the action moment.**

Six development principles. Ten enforcement hooks. Two slash commands. Skills + agents. Packaged from real-world AI-assisted engineering delivery, distributed under MIT so any team can fork, adapt, and improve.

---

## What this is

If you're using AI agents (Claude Code, Codex, or any LLM-coding tool) to ship real production software, you've encountered the proxy-vs-behavior problem: tests green, CI green, deploy succeeded, but the change isn't actually doing what was asked.

This plugin closes that gap. Not by promising better AI; by encoding the discipline that catches the gap — at the moment work is being done, not at the moment a customer reports the bug.

**Three layers:**

| Layer | What it does | How |
|---|---|---|
| **Convention** | Names the principles every action should honour | The Six Principles, loaded as a skill at session start |
| **Tooling** | Fires at the action moment to enforce the principles | 10 hookify rules + 2 slash commands |
| **Specialised review** | Domain expertise on demand | SME agents (marketing, pharma template design — extend with your own) |

You don't have to remember the rules. The hooks make them visible at the right time.

---

## The Six Principles

1. **Think before coding** — state your assumptions; ask when unsure; never guess
2. **Simplicity first** — minimum code that solves the problem; no unrequested abstractions
3. **Surgical changes** — every changed line traces back to the originating ask
4. **Goal-driven execution** — verifiable success criteria written *before* any action; failing test reproduces the bug before the fix, acceptance list precedes the feature
5. **Verify the behavior, not the proxy** — tests green is not behavior verified; reproduce the user-facing outcome
6. **Finish what you started** — no mid-flight amendments to in-flight work; if new scope can't wait, spawn a new dispatch rather than interrupt the current one

Read the full treatment: [`skills/six-principles.md`](skills/six-principles.md).

---

## What's in the box

**`hooks/`** — 10 hookify rules that fire on specific events:

| Rule | Fires on | Purpose |
|---|---|---|
| `enforce-dispatch-discipline` | Writing a worker dispatch file | Verify file references + factual claims before spawning a worker |
| `require-unverified-section` | Writing a `.reports/*.md` | Enforce explicit proxy-vs-behavior accounting |
| `require-simplify-before-commit` | `git commit` | Reminder to run `/simplify` (Principle #2) |
| `require-build-before-push` | `git push` | Reminder to verify the build before pushing |
| `require-live-validation` | Session stop | Reminder that "done" needs live evidence, not just proxy |
| `check-deploy-after-merge` | `gh pr merge` | Walk the deploy verification: CI + bundle hash + functional spot-check |
| `warn-direct-main-push` | `git push origin main` | Flag whether direct-to-main is appropriate for this change |
| `require-schema-regen` | Editing a SQL migration | Reminder to regenerate typed schema constants |
| `require-check-constraint-audit` | Adding CHECK constraints | **Blocks** until value-set is verified against all writers (prevents prod-500 class of bug) |
| `warn-raw-sql-columns` | Raw string column names in SQL | Steer toward typed constants |

**`commands/`** — slash commands you invoke directly:

- `/install-hooks` — One-shot copy of plugin rules into your hookify namespace (run once after install)
- `/uninstall-hooks` — Pairs with `/install-hooks` for clean removal / updates
- `/dispatch` — Scaffolds a compliant worker dispatch file
- `/verify` — Walks a structured live-verification of recently-shipped work

**`skills/`** — discipline narratives loaded into context:

- `six-principles` — the canonical principle treatment
- `onboarding` — new-user orientation

**`agents/`** — specialised review agents:

- `marketing-strategy-sme` — senior pharma-marketing reviewer (15+ years HCP creative experience)
- `pharma-template-designer-sme` — Veeva PromoMats-fluent template designer

Add your own agents for the SMEs your team needs.

---

## Install

### Prerequisite — install Anthropic's `hookify` plugin

This plugin **relies on** Anthropic's `hookify` plugin (from `claude-plugins-official`) for hook discovery and rule evaluation. We do not reimplement that machinery. Verify or install it first:

```bash
claude plugin list | grep hookify
# If not present:
claude plugin install hookify
```

### Install this plugin

```bash
# Once the marketplace listing is live:
claude plugin install The-Ai-Expert/discipline

# Or directly from this repo:
git clone https://github.com/The-Ai-Expert/discipline.git ~/.claude/plugins/cache/local/theaiexpert-discipline
```

### Wire the hookify rules into your environment

After the plugin is installed, run **`/install-hooks`** once. This copies the plugin's ten `hookify.theaiexpert-*.local.md` rules into `~/.claude/` — the namespace where hookify discovers them at runtime.

```
/install-hooks
```

Then **restart Claude Code** so hookify picks up the new rules.

### Confirm

- Try writing a dispatch file at `.reports/dispatches/dispatch_test.md` — the `theaiexpert-enforce-dispatch-discipline` rule should fire with the dispatch checklist
- Run `git commit` in any project — the `theaiexpert-require-simplify-before-commit` rule should fire

### Why the manual `/install-hooks` step

Anthropic's `hookify` plugin discovers rules via a glob on `~/.claude/hookify.*.local.md` — it does not scan plugin install paths. To make plugin-provided rules discoverable, they must live in that namespace. `/install-hooks` does the one-time copy idempotently; `/uninstall-hooks` reverses it cleanly. We deliberately do not write our own hook discovery machinery — hookify is the canonical Anthropic primitive and we rely on it entirely.

---

## Status: early — testing in progress

This plugin is **v0.1.0** and actively seeking real-world test feedback before we recommend wider adoption. If you've installed it: **welcome, and thank you**. We want to hear what works, what's confusing, and what's broken. See [Test feedback](#test-feedback) below.

---

## Try it — tester smoke checklist

After running through the [Install](#install) section, walk this checklist to confirm the plugin actually works in your environment. This is the verification that the install succeeded — proxy-vs-behavior in action (Principle #5).

### Smoke test 1 — Hook discovery

Open a fresh Claude Code session (so hookify re-scans rules) and run:

```bash
ls ~/.claude/hookify.theaiexpert-*.local.md | wc -l
# Expected: 10
```

If you see a number other than 10, `/install-hooks` either didn't run or didn't complete. Re-run `/install-hooks` and check the output.

### Smoke test 2 — `enforce-dispatch-discipline` hook fires

In any project, write a dummy dispatch file:

```bash
mkdir -p .reports/dispatches
cat > .reports/dispatches/dispatch_smoke_test.md <<'EOF'
# Dispatch: smoke test
This is a dispatch file written to verify the hook fires.
EOF
```

The `theaiexpert-enforce-dispatch-discipline` hook should fire and show the dispatch checklist warning. If it doesn't fire, hookify either isn't installed/enabled, or the rule didn't load. Run `claude plugin list | grep hookify` to confirm hookify is enabled.

### Smoke test 3 — `/dispatch` slash command resolves

In a fresh prompt, type `/dispatch` and press Tab. Claude Code should offer it as an autocomplete option. Invoke it. The full dispatch template should render.

### Smoke test 4 — Six Principles skill loads

Ask Claude `"What are the Six Principles?"` — Claude should be able to recite them with the canonical mechanics + failure modes per principle. The `skills/six-principles.md` content is the source of truth.

### Smoke test 5 — `require-unverified-section` hook fires

In any project, write a `.reports/test-report.md` file that doesn't contain the word "Unverified" — the hook should fire and remind you to add the `## Verified` / `## Unverified — reviewer please decide` shape.

If all five smoke tests pass, the plugin is functioning end-to-end in your environment. 🎯

---

## Test feedback

If you're an early tester: please file findings as a GitHub issue.

**File a structured test report:** [Open a new issue with the test-feedback template](https://github.com/The-Ai-Expert/discipline/issues/new?template=test-feedback.md) — or just open a free-form issue at [github.com/The-Ai-Expert/discipline/issues](https://github.com/The-Ai-Expert/discipline/issues).

**What we most want to know:**

1. **Install friction** — was anything confusing in the install + `/install-hooks` flow? Did any step assume context you didn't have?
2. **Hook noise** — are any of the 10 rules firing too often, too rarely, or with messages that aren't actionable?
3. **README clarity** — what would a fresh reader want explained that we left implicit?
4. **Workflow fit** — does the Workflow 1/2/3 in Quick Start match how you actually ship work? Where does it break?
5. **Skill / agent surfaces** — did the Six Principles skill actually surface when relevant? Did the SME agents load when invoked?

Be specific. Quoting the exact error message or the exact line of README that confused you is more useful than "X is broken."

If something is just plain wrong — open an issue and we'll fix it.

---

## Quick start

Three workflows the plugin enables:

### Workflow 1 — Orchestrator dispatches work to a worker

```bash
# 1. Brainstorm with stakeholder, write down success criteria
# 2. Invoke /dispatch — scaffold a compliant dispatch file
# 3. Save to .reports/dispatches/dispatch_<descriptive>_<date>.md
# 4. enforce-dispatch-discipline hook fires — verify the boxes
# 5. Hand the file to a background worker (claude --bg or equivalent)
```

### Workflow 2 — Worker ships a change

```bash
# 1. Read the dispatch
# 2. Write a failing test first (Principle #4)
# 3. Implement minimum diff (Principle #2)
# 4. Run /simplify — hook reminds you at commit time
# 5. Push — hook reminds you to verify the build
# 6. PR — require-unverified-section hook enforces report shape
# 7. Adversarial review (Codex or peer)
# 8. Merge — check-deploy-after-merge hook walks deploy verification
# 9. /verify — structured live-verification walk
# 10. Write report at .reports/*.md with Verified + Unverified sections
```

### Workflow 3 — Independent solo work

You don't have to be running an orchestrator/worker split to benefit. The hooks fire on every action regardless of who's at the keyboard. The principles apply to one-person teams too.

---

## Customise

- **Add a project-specific hook:** create `~/.claude/hookify.<name>.local.md` — it coexists with plugin hooks
- **Override a plugin hook:** create `~/.claude/hookify.<plugin-rule-name>.local.md` with `enabled: false`
- **Add a project-specific SME agent:** drop it in `<project>/.claude/agents/<name>.md`
- **Tighten / loosen a rule:** fork the plugin, edit the hook, send a PR

---

## Philosophy

The cost of these hooks is small: a few seconds of friction per action moment. The cost of the bugs they prevent is large: a few hours per "wait, that wasn't supposed to ship" incident. Breakeven is roughly the third re-work loop on any non-trivial feature.

The principles aren't novel; they're a synthesis of practices any serious software team converges on. What this plugin contributes is **enforcement at the action moment** — making the rules visible at the keyboard, not just in a wiki page nobody re-reads.

---

## Contributing

Issues, PRs, forks all welcome. If your team has a discipline iteration that generalises, send a PR upstream.

The principles themselves are not for negotiation. The enforcement mechanisms are.

---

## License

MIT. Use it freely. Attribution preserved per the LICENSE file.

---

## Author + contact

Built by **The Ai Expert** — AI engineering consultancy.

- GitHub org: [@The-Ai-Expert](https://github.com/The-Ai-Expert)
- Lead: Erik Schwartz · [@ESchwaa919](https://github.com/ESchwaa919) · eschwaa@gmail.com

If you're shipping AI-assisted software and the discipline gap is biting you, open an issue or reach out. Happy to talk.
