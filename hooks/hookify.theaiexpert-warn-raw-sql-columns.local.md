---
name: theaiexpert-warn-raw-sql-columns
enabled: true
event: file
action: warn
conditions:
  - field: file_path
    operator: regex_match
    pattern: (api|server|backend|src)/.*/(functions|handlers|routes)/.*\.(ts|js|py)$
  - field: new_text
    operator: regex_match
    pattern: (SELECT|INSERT|UPDATE|ORDER BY|WHERE|JOIN ON).*'[a-z_]+'
---

**[warn-raw-sql-columns]**

**Raw string column names in SQL detected.**

Prefer typed column constants over string literals:

```typescript
// Good
import { Cols } from '../schema/columns.js';
const sql = `SELECT ${Cols.Documents.processing_status} FROM Documents`;

// Bad
const sql = `SELECT 'processing_status' FROM Documents`;
```

(Equivalent patterns exist for Python — typed namespaces, sqlalchemy declarative, etc.)

**Why:** hallucinated or stale column names are a common production-500 source — typos like `description` vs `summary`, `created_at` vs `uploaded_at`. The compiler / lint can catch the typed form; it can't catch the string form. Use a generation script + a typed constants module to make these errors statically detectable.

Acceptable raw-string uses: schema migrations themselves, ad-hoc scripts, one-off backfills.
