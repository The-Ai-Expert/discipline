---
name: require-unverified-section
enabled: true
event: file
action: warn
tool_matcher: Edit|Write|MultiEdit
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.reports/.*\.md$
  - field: new_text
    operator: not_contains
    pattern: Unverified
---

**[require-unverified-section]**

This `.reports/*.md` file is missing the mandatory "Unverified — reviewer please decide" section.

Required shape for every worker report and PR body:

```markdown
## Verified
- [what behavior, in what environment, with what evidence]

## Unverified — reviewer please decide
- [what I did NOT check]
- Residual risk: HIGH | MED | LOW
- If the gap hides a bug, symptom would be: [one sentence]
```

**Why:** Principle #5 (verify behavior not proxy). Tests + tsc + lint + CI green are all proxies for working behavior, not proof of it. The "Unverified — reviewer please decide" section is the forcing function — it requires the author to enumerate their proxy/behavior gap explicitly.

If the section legitimately has nothing to list, write:
> - Nothing material unverified. Residual risk: LOW. No plausible behavior gap given [evidence].

…so the reviewer sees you considered the perimeter.
