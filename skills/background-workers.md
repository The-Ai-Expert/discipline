---
name: background-workers
description: Background agent workers must be non-interactive BY CONSTRUCTION - any permission prompt hangs a headless session forever. Three enforcement layers (CLI disallow, hang-watchdog, BLOCKED-file self-alert), the canonical dispatch header, why state.json is not a liveness signal, and why parallel mutating workers need separate git worktrees. Use when dispatching any background worker, when a worker seems stuck, or when running more than one worker in a repo.
---

# Background workers: non-interactive by construction

A background (headless) agent session has no human at the keyboard. **Any** interactive gate — a permission prompt, a confirmation dialog — blocks it forever, and the backgrounding *masks* the hang as "still working". Every rule in this skill exists because a real worker sat frozen at a prompt while its status said `working`.

> Vendor note: concrete flags and paths below (`claude --bg`, `--disallowedTools`, `state.json`) are **Claude Code's spellings** of universal concepts. Translate to your harness: every headless-agent runtime has *some* way to restrict tools at spawn, *some* status metadata that lags, and *some* log you can grep. The three layers apply regardless of vendor; the incantations differ.

## The three layers (use all three)

**1. Enforce at the CLI.** Make the hanging tools *unavailable*, not merely forbidden. Prose in a dispatch ("do not call X") is a request; the model can still call X — verified twice in one week. Disallow the interactive-prone tools at spawn (e.g. worktree-switching tools), and never rely on a blanket permissions-bypass that also drops your merge/push guardrails.

> ⚠ Flag-parsing gotcha: variadic flags (`--disallowedTools <tools...>`) greedily eat the next argument. The space form can swallow your entire dispatch prompt — the worker spawns idle with no task, a silent no-op. Use the `=` form (`--disallowedTools=ToolName`) and eyeball the spawn output.

**2. Detect with a hang-watchdog.** For tools you didn't think to disallow: a standing monitor greps the worker's log for the interactive tell ("Do you want to proceed") and alerts within seconds. This is the safety net for the unknown-unknowns.

**3. Give the worker a self-alert path.** The dispatch instructs: if you hit a decision you can't resolve from grounding, make the grounded assumption and *flag it* in your report. Only if truly blocked, write `worker-status/<id>-BLOCKED.md` with the specific question and **stop**. A frozen prompt never lets a model reach a self-alert — which is why layers 1 and 2 exist.

## The canonical dispatch header

The rule must live **in the dispatch text** — a worker will not read your team's memory. First paragraph of every background dispatch:

> ⛔⛔ CRITICAL ENV RULE — READ FIRST: You are a BACKGROUND worker, ALREADY in `<worktree-path>` on branch `<branch>`. WORK IN PLACE. Do NOT create/switch/enter any worktree or cd elsewhere — an interactive prompt HANGS a bg session forever. If genuinely blocked, write `worker-status/<your-id>-BLOCKED.md` with the question, commit a one-line `docs: BLOCKED — <reason>`, and STOP; do not sit on a prompt.

## Status files lie: monitor the work, not the metadata

`state.json` (or any harness status field) **lags** — it reads `working` straight through a hang *and* long past a worker's terminal state. It is neither a liveness signal nor a done signal. Monitor the signals that lead:

- **A report/worksheet/BLOCKED commit = check now.** A worker committing its report is wrapping up — done or escalating. Inspect immediately; don't wait for a status field to catch up.
- **Commit-quiet after activity = inspect.** Once a worker has committed at least once, minutes of silence means done, blocked, or stuck — never "leave it".
- **The log-prompt grep** — the watchdog from layer 2.
- **The BLOCKED-file directory** — the worker's own voice. And in the dispatch, require a blocked worker to BOTH write the file AND commit a one-line `docs: BLOCKED — <reason>`, so the escalation is visible in git even if the file path is missed.
- **Actively poll** on a cadence. Real incident: a worker finished, committed its report, and correctly went to blocked — but escalated inside the report instead of the BLOCKED file, while the status field lagged on "working". It sat idle-blocked for 40 minutes until a human asked. Two redundant blind spots, one wasted hour-class: monitor what leads.

Corollary for anything that *backgrounds a child process* (review wrappers, long tools): redirect the child's stdin/stdout/stderr to files or /dev/null. A backgrounded child that inherits the launcher's streams holds pipes open — callers hang waiting for EOF that never comes. Two real bugs in one day came from this one class.

## Parallel mutating workers need separate worktrees

"They touch different files" does **not** make two workers safe in one checkout. Different files protects file *contents*; both workers still share **one git index and one branch HEAD**. Real near-miss: two parallel workers in one checkout, one ran `git commit --amend`, and the sibling's in-progress edits were silently bundled into the wrong commit — recovered only by luck and a reflog.

Rule: mutating workers run **sequentially** in a checkout, or each gets its **own `git worktree`** (pre-created by the orchestrator, worker spawned with its cwd already inside — never ask the worker to create/enter one, that's the hanging prompt again). Read-only agents alongside one writer are fine.

## Completion is a report, not a toast

A finished worker's output lands in the session that spawned it — nowhere else. The owning orchestrator **must surface a short outcome summary** (what was verified, what wasn't, what's flagged) to the human before moving on. A completion notification with no digest is a mystery, not a report.
