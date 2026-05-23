---
description: Remove the plugin's hookify rules from your local hookify namespace. Pairs with /install-hooks. Leaves your personal rules untouched.
---

# /uninstall-hooks — Remove discipline hooks from hookify

Removes the plugin's `theaiexpert-*` hookify rules from `~/.claude/`. Idempotent — safe to run if nothing is installed.

## What this command does

```bash
TARGET_DIR="${HOME}/.claude"

removed=0
for f in "$TARGET_DIR"/hookify.theaiexpert-*.local.md; do
  if [ -f "$f" ]; then
    rm "$f"
    removed=$((removed + 1))
  fi
done

echo "✅ Removed $removed theaiexpert-* hookify rules from $TARGET_DIR"
echo "   Restart Claude Code to clear the rules from active hook discovery."
```

Only files matching `hookify.theaiexpert-*.local.md` are removed. Your own personal `hookify.*.local.md` rules are untouched.

## After running

Restart Claude Code. Hookify won't discover the removed rules on the next session.

## Why pair with `/install-hooks`

Clean install/uninstall pairing means users can update to a new version of the plugin's rules without manual cleanup: `/uninstall-hooks` + `claude plugin update` + `/install-hooks` gives a clean slate.
