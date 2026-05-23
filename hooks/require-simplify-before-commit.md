---
name: require-simplify-before-commit
enabled: true
event: bash
pattern: git\s+commit
action: warn
---

**[require-simplify-before-commit]**

**Did you run `/simplify`?**

Before committing feature or enhancement code, run `/simplify` to check for:
- Code reuse opportunities
- Quality issues
- Efficiency improvements
- Premature abstractions / over-engineering

Fix anything it finds before committing. Tied to Principle #2 (simplicity first) — the commit-time forcing function for "minimum code that solves the problem."

Only skip for build fixes, hotfixes, and pure documentation changes.
