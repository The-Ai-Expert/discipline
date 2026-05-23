---
name: require-schema-regen
enabled: true
event: file
action: warn
conditions:
  - field: file_path
    operator: regex_match
    pattern: migrations/.*\.sql$
---

**[require-schema-regen]**

**Migration detected.** After applying this migration:

1. **Regenerate any typed column / schema constants** from the live schema (most projects have a script — `generate-schema.js`, `pg_dump`-to-types, Prisma migrate, etc.).
2. **If CHECK constraints are involved**, audit all code paths that write to the constrained columns. See `require-check-constraint-audit`.
3. **Test every enum value** against the live DB before deploying.
4. **Commit the regenerated schema constants** in the same PR as the migration. Drift between migration and consumers is a top failure mode.

**Why:** type drift between the live schema and the consuming code is a common production incident pattern. Catching it at the migration-author stage is cheap; catching it at the production-500 stage is expensive.
