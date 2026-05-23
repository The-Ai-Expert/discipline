---
name: require-check-constraint-audit
enabled: true
event: file
action: block
conditions:
  - field: new_text
    operator: regex_match
    pattern: ADD\s+CONSTRAINT.*CHECK|CHECK\s*\(
---

**[require-check-constraint-audit]**

**BLOCKED: CHECK constraint detected in a migration.**

Before adding a CHECK constraint, you MUST:

1. **Query distinct values currently in the column** in the live DB. Every value present must be in the constraint.
2. **`grep` ALL code paths that write to this column.** Every literal, default, or parameter that flows in must be in the constraint.
3. **UNION the two sets.** That's your allowlist. Anything missing from either fails at apply time.
4. **Test every value with a rollback INSERT** before applying to production.
5. **Update any TypeScript / Python enums** to match the constraint values exactly.

**Why blocked, not warned:** CHECK constraints that ship with incomplete value-sets produce production 500s that are hard to revert. The audit is cheap; the production incident is expensive. The combinatorial coverage matters.

Override only if you've completed steps 1-5 and the constraint is truly limited to a value-set you've verified is exhaustive.
