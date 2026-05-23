---
name: theaiexpert-require-build-before-push
enabled: true
event: bash
pattern: git\s+push
action: warn
---

**[require-build-before-push]**

**Did you run the build before pushing?**

Verify the build passes locally before pushing, to avoid CI-triggered deployment failures and waste of cycles for downstream reviewers / CI:

- TypeScript projects: `tsc --noEmit` + `npm run build`
- Python projects: `pytest` / `ruff check` / project-specific equivalent

This is a warning, not a block — push if you have already verified the build.

For docs-only commits (specs, reports, markdown), no build needed.
