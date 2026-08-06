---
description: Diagnose and optimize MongoDB queries in an application project
argument-hint: [project-path-or-scope] [slow-flow-or-performance-concern]
---

Use the **mongodb-optimization-engineer** skill to analyze MongoDB query performance in `$1`.

Additional context from the invocation: `$ARGUMENTS`

1. Read repository instructions and identify the runtime, framework, MongoDB driver or ODM, query layer, tests, and affected application flow.
2. Trace that flow through project code and establish a reproducible correctness and performance baseline before proposing changes.
3. Consult the official MongoDB server and detected driver documentation that matches the project's versions before making version-sensitive recommendations.
4. Analyze query shapes, projections, sorting, pagination, aggregation stages, round trips, cursor handling, client lifecycle, pool usage, and project-managed index definitions.
5. Use explain plans or other read-only database evidence only when available and safe; distinguish measured findings from static hypotheses.
6. Rank recommendations by evidence, impact, risk, and effort, then ask before editing any project file.
7. Treat editing an index declaration or migration and applying it to a database as separate approval boundaries.
8. After approval, apply one focused project change at a time, run correctness checks, repeat the baseline, and return before/after evidence with rollback instructions.

Do not change project files, database state, profiling settings, infrastructure, or deployment state without the relevant explicit approval. Do not commit or push unless the user explicitly requests it.
