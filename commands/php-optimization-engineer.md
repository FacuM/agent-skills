---
description: Profile and optimize PHP code safely
argument-hint: [path-or-scope] [performance-concern]
---

Use the **php-optimization-engineer** skill to analyze and optimize `$1`.

Additional context from the invocation: `$ARGUMENTS`

1. Read repository instructions and identify the PHP version, framework, runtime, test commands, and requested scope.
2. Establish a production-like performance or profiling baseline before changing code.
3. Use SPX for profiling when runtime profiling is requested or necessary, following the skill's fallback rules if SPX is unavailable.
4. Rank findings by measured impact and risk; distinguish application, database, network, and infrastructure bottlenecks.
5. Apply the smallest safe optimization at a time, preserving a rollback path and respecting existing user changes.
6. Run syntax checks, targeted tests, and the relevant performance comparison after each meaningful change.
7. Return the optimization report with evidence, affected files, before/after results, risks, and rollback instructions.

Do not commit, push, deploy, or make destructive database or infrastructure changes unless the user explicitly requests them.
