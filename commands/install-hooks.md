---
description: One-shot install of the plugin's hookify rules into your local hookify namespace. Idempotent — re-run safely to update. Requires the `hookify` plugin from claude-plugins-official to be installed and enabled.
---

# /install-hooks — Install discipline hooks into hookify

This command copies the plugin's ten `theaiexpert-*` hookify rules from the plugin install path into `~/.claude/hookify.theaiexpert-*.local.md` — the namespace the `hookify` plugin discovers at runtime.

The rules live with the `theaiexpert-` prefix so they coexist with your existing personal rules without collision.

## Prerequisite

The Anthropic `hookify` plugin must be installed and enabled. Verify with `claude plugin list` — if `hookify@claude-plugins-official` isn't enabled, run `claude plugin install hookify` first.

This plugin does **not** reimplement hook discovery — it relies entirely on hookify for that. We just provide the rule content + a one-shot install step.

## What this command does

When invoked, execute the following bash logic:

```bash
PLUGIN_HOOKS_DIR="${CLAUDE_PLUGIN_ROOT}/hooks"
TARGET_DIR="${HOME}/.claude"

if [ ! -d "$PLUGIN_HOOKS_DIR" ]; then
  echo "❌ Plugin hooks directory not found at $PLUGIN_HOOKS_DIR"
  exit 1
fi

mkdir -p "$TARGET_DIR"

# Idempotent copy — overwrites any prior install but leaves user's other hookify rules untouched
copied=0
for f in "$PLUGIN_HOOKS_DIR"/hookify.theaiexpert-*.local.md; do
  if [ -f "$f" ]; then
    cp "$f" "$TARGET_DIR/$(basename "$f")"
    copied=$((copied + 1))
  fi
done

echo "✅ Installed $copied theaiexpert-* hookify rules into $TARGET_DIR"
echo "   Restart Claude Code for hookify to pick up the new rules."
```

## After running

Restart Claude Code (`Cmd+Q` then relaunch, or exit/restart your terminal session). Hookify will re-scan rules on the next session start.

To verify install worked, try writing a file to `.reports/dispatches/dispatch_test.md` — the `theaiexpert-enforce-dispatch-discipline` rule should fire with the dispatch checklist warning.

## To uninstall

Run `/uninstall-hooks` — pairs cleanly with this command.

## Why this approach

Anthropic's `hookify` plugin discovers rules via a cwd-relative glob (`.claude/hookify.*.local.md`) — it does NOT scan plugin install paths. To make plugin-provided rules discoverable, they must live in the same namespace as user-authored rules: `~/.claude/hookify.*.local.md`. This command does the one-time copy idempotently.

We deliberately don't try to reimplement hook discovery, post-install hooks, or any of hookify's machinery — we rely on the Anthropic plugin entirely. This command is a thin install bridge, nothing more.

## Tied principle

Principle #2 (Simplicity first) — minimum code that solves the problem. We could have written a parallel hook system. We didn't. We use the one that already works.
