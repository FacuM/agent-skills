---
name: mongodb-optimization-engineer
description: Diagnose and optimize MongoDB query performance inside application projects by tracing slow flows through project code, measuring query behavior, reviewing driver or ODM usage, aggregation pipelines, projections, pagination, round trips, and index requirements, then proposing and validating focused fixes. Use when a project has slow MongoDB queries, excessive database calls, inefficient aggregations, N+1 access, unbounded reads, deep pagination, over-fetching, connection misuse, index-related regressions, or a request to profile or debug MongoDB-backed application performance.
---

# MongoDB Optimization Engineer

Analyze MongoDB performance from the application project's perspective. Trace the affected application flow, establish evidence, identify inefficient query or driver behavior, and propose the smallest safe change. Treat the database deployment as a supporting evidence source, not as the primary optimization target.

## Operating Boundaries

- Target application repositories and their MongoDB access patterns.
- Start read-only in both the repository and the database.
- Diagnose and present a proposed patch before modifying project files.
- Ask for explicit approval before editing query code, driver configuration, tests, index declarations, or migrations.
- Treat editing a project-managed index artifact and applying it to a database as separate approval boundaries.
- Never create or drop indexes, alter data, enable profiling, kill operations, change database configuration, deploy, commit, or push without explicit approval for that action.
- Preserve existing user changes. Do not reset, overwrite, or include unrelated work.
- Escalate standalone capacity, storage, replication, sharding, or server tuning findings to the appropriate operator or DBA.

## Prerequisites

Use the project's existing runtime, dependency manager, test suite, and benchmark or tracing tools. Database access is optional for static analysis, but runtime claims require reproducible application evidence or read-only database evidence.

When database access is available:

- Prefer a read-only account with access only to the necessary namespaces and diagnostic data.
- Redact credentials, connection strings, query literals, tenant identifiers, and document contents from reports.
- Record the MongoDB server version and topology before interpreting version-dependent explain output.
- Do not install tools, alter profiling settings, or change the target environment merely to gather evidence without approval.

## Consult Current MongoDB Guidance

Before making version-sensitive recommendations, consult the official MongoDB documentation that matches the detected server and driver versions. Start with:

- [Query Optimization](https://www.mongodb.com/docs/manual/core/query-optimization/) for selectivity, limits, projections, covered queries, and server-side operations
- [Analyze Query Performance](https://www.mongodb.com/docs/manual/tutorial/evaluate-operation-performance/) and [Explain Results](https://www.mongodb.com/docs/manual/reference/explain-results/) for the current diagnostic fields and caveats
- [Aggregation Pipeline Optimization](https://www.mongodb.com/docs/manual/core/aggregation-pipeline-optimization/) for optimizer rewrites, index-eligible stages, and engine-specific behavior
- [ESR Guideline](https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-guideline/) and [Compound Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-compound/) for candidate index order and prefix behavior
- [Connection Pool Overview](https://www.mongodb.com/docs/manual/administration/connection-pool-overview/) plus the detected driver's own documentation for client lifecycle and supported pool options
- [Write Operation Performance](https://www.mongodb.com/docs/manual/core/write-performance/) for the write and storage costs of additional indexes

Treat documentation as versioned evidence, not as permission to make changes. Prefer official server, driver, and ODM documentation over blogs or generic rules. If project behavior conflicts with current documentation, reproduce the behavior and report the discrepancy rather than forcing a presumed optimization.

## Workflow

### Step 1: Assess the Project and Reported Flow

1. Read repository instructions and inspect the current git status and diff.
2. Identify:
   - Language, framework, runtime, and package manager
   - MongoDB driver, ODM, repository layer, and their versions
   - Client construction and dependency-injection lifecycle
   - Query, aggregation, schema, and index-definition locations
   - Test, benchmark, lint, and type-check commands
   - Relevant logging, tracing, APM, or query-count instrumentation
3. Define the affected application flow:
   - HTTP endpoint, command, worker, scheduled job, or event handler
   - Input that reproduces the problem
   - Expected result set, ordering, pagination, and consistency behavior
   - Observed latency, throughput, timeouts, memory pressure, or query count
4. Establish the requested scope: one query, one application flow, one module, or a broader query-access audit.
5. Ask only for information that cannot be discovered from the repository or supplied evidence.

### Step 2: Trace the Data-Access Path

Follow the slow flow from its entrypoint to MongoDB. Record each call site and query shape rather than focusing only on the final database operation.

Look for:

- Per-document lookups and N+1 repository calls
- Duplicate queries within the same request or job
- Sequential round trips that can be combined or safely batched
- Query construction that changes shape unnecessarily
- Filters, sorts, projections, limits, collation, hints, and read preferences
- Aggregation pipelines and nested `$lookup` pipelines
- Cursor materialization, batch size, streaming, and premature conversion to arrays
- Client, session, transaction, and connection-pool lifecycles
- Retry behavior that multiplies work or hides timeouts
- Application-side filtering, sorting, grouping, or joining that MongoDB could perform more efficiently

Document whether the bottleneck is likely in project code, the query/index relationship, result processing, network round trips, or external database infrastructure.

### Step 3: Establish a Reproducible Baseline

Measure before recommending changes. Use a production-like dataset and execution path when safely available.

Capture the metrics relevant to the symptom:

- End-to-end latency and, when available, p50/p95/p99 latency
- Query count and database round trips per application operation
- Documents returned, result payload size, and cursor batches
- Execution time, keys examined, and documents examined
- CPU, memory, or event-loop/runtime impact in the application
- Throughput and error or timeout rate

Repeat enough runs to reduce warm-up and cache noise. Keep inputs, dataset state, concurrency, environment, and measurement method consistent. Do not claim an improvement from a single incomparable timing.

If the flow cannot be run, continue with static analysis but label every performance conclusion as a hypothesis and provide the exact command or scenario needed to validate it.

### Step 4: Analyze Project Query Patterns

#### Query Shape and Result Handling

Check for:

- Missing selective predicates or unbounded collection reads
- Fetching full documents when a stable projection is sufficient
- Missing limits or unintentionally large result sets
- Deep `skip` pagination that should use a stable range/cursor key with a unique tie-breaker
- Regex, `$ne`, `$nin`, `$exists`, type mismatch, or computed predicates that reduce index usefulness
- Sorting that cannot be satisfied by the candidate index
- Permanent `hint` usage added without evidence that it is safer than normal plan selection
- Multiple queries that can become one set-based query without changing semantics
- `find`/`findOne`, count, distinct, or update operations that do more work than required
- Read-modify-write sequences that can use an atomic server-side update operator without changing business rules
- Client-side filtering, sorting, grouping, or deduplication of large datasets
- Converting cursors to arrays before streaming or bounded consumption can occur
- Unnecessary serialization, hydration, populate, or lean/non-lean object conversion

Preserve correctness. Do not remove fields, sorts, consistency guarantees, collation, or transaction boundaries without proving they are unnecessary.

#### Aggregation Pipelines

Check for:

- `$match` stages that can be moved earlier without changing results
- Filters that depend on computed fields and therefore cannot be moved before their computation
- Blocking `$sort`, `$group`, `$setWindowFields`, or `$facet` stages over excessive input
- `$unwind` fan-out and `$lookup` cardinality growth
- Missing indexes for the local and foreign predicates used by `$lookup`
- Expressions that prevent predicate or sort pushdown
- Repeated computation suitable for application reuse or a separately approved materialization strategy
- `allowDiskUse` being used to mask an inefficient pipeline rather than as an intentional safeguard

Inspect the optimized explain plan before manually reordering stages. MongoDB already moves some filters, coalesces some limits, and prunes unused fields; adding an early `$project` solely to reduce document width is often redundant. Measure the whole pipeline and the cardinality after expensive stages. Do not optimize a stage in isolation while increasing total work elsewhere.

#### Driver, ODM, and Repository Usage

Check for:

- Creating a new MongoDB client for each query, loop iteration, request, or job item
- Misaligned connection-pool sizes, wait timeouts, or idle settings
- Sequential awaits where a single query, bulk operation, or bounded concurrency is correct
- Per-document inserts, updates, or deletes that should use bulk operations
- ODM hooks, virtuals, population, middleware, or automatic index behavior that adds hidden work
- Retried operations that are not idempotent or that amplify load
- Long-lived transactions, overly broad sessions, or unnecessary transaction use
- Read or write concerns stronger than the application requirement

Each `MongoClient` normally owns connection pools for the servers it uses. Confirm the detected driver's documented lifecycle and defaults before changing pool settings; do not copy option names or thresholds from another driver. Prefer the driver's supported APIs and the project's established abstractions. Do not bypass repositories or ODMs solely for micro-optimizations unless measurements justify the maintenance cost.

#### Index Requirements

Derive index recommendations from actual query shapes and workload evidence. For each recommendation, record:

- Namespace and representative query shape
- Equality, sort, and range fields with the proposed key order
- Projection or covered-query opportunity, if relevant
- Expected benefit and affected write/storage cost
- Multikey, collation, partial-filter, sparse, TTL, wildcard, or uniqueness constraints
- Similar or redundant existing indexes
- Validation plan and rollback approach

Use the ESR guideline as a starting heuristic for compound keys, then validate it against the actual query, sort, range selectivity, and workload. Verify compound-index prefix behavior and direction requirements. For a proposed covered query, include every filter and projected field required by the operation and account for `_id`, multikey, sharding, and `null` limitations.

Do not recommend indexes from field names alone. Do not assume an index is beneficial until its selectivity, workload frequency, sort needs, existing prefixes, and write amplification have been considered. Every additional index consumes storage and adds work to inserts, deletes, and some updates.

### Step 5: Gather Optional Read-Only Database Evidence

Use database evidence to confirm or reject project-level hypotheses. Prefer existing slow-query logs, APM traces, or supplied profiler output before requesting new database instrumentation.

When permissions and version support allow, use `$indexStats` only as contextual evidence. Its counters are node-local, can reset after restart or index modification, and do not include internal operations; a zero counter alone does not prove an index is safe to remove. Use `$currentOp` only to observe relevant long-running work, never to terminate it within this workflow.

For explain analysis:

1. Reproduce the exact project query shape, including sort, projection, limit, collation, and hint.
2. Start with `queryPlanner` when executing the query could be expensive or unsafe.
3. Use `executionStats` only when the operation, dataset, and target environment make execution safe.
4. Compare `nReturned`, `totalDocsExamined`, `totalKeysExamined`, stages, bounds, sort behavior, and indexes used.
5. Remember that explain bypasses the plan cache and its timing may not represent steady-state application latency.
6. Interpret fields defensively because explain output varies by MongoDB version and execution engine.

Never enable the database profiler as a default debugging step. It can add performance and storage overhead. If existing evidence is insufficient, explain the risk and request explicit approval before changing profiling settings. Do not use profiling level 2 as a routine shortcut.

If server metrics indicate saturation, storage latency, replication lag, chunk imbalance, or another operational bottleneck, document the evidence and return it as an external dependency rather than tuning the server within this skill.

### Step 6: Rank Findings and Request Approval

Rank findings by:

- **Critical:** Causes timeouts, failures, runaway work, or severe user-visible impact
- **High:** Measured bottleneck with a substantial expected improvement
- **Medium:** Repeated inefficiency with meaningful cumulative cost
- **Low:** Minor cleanup or an unmeasured opportunity

For each proposed project change, provide:

- Evidence and affected application flow
- File, symbol, and query shape
- Proposed edit and expected benefit
- Correctness, compatibility, and rollout risks
- Validation command and rollback method
- Whether it changes only project code, an index artifact, or the live database

Ask for approval before editing. If an index is required, first recommend it; ask whether to edit the project-managed declaration or migration. Applying that artifact to any database requires separate approval.

### Step 7: Apply Approved Project Changes

After approval:

1. Make one focused change at a time.
2. Avoid unrelated refactors and formatting churn.
3. Add or update correctness tests for filters, ordering, pagination boundaries, empty results, duplicates, and error behavior.
4. Run the narrowest relevant test and static checks.
5. Repeat the original performance measurement under comparable conditions.
6. Keep the change only when correctness is preserved and the result is supported by evidence.
7. Record the diff and rollback instructions.

Do not commit or push unless the user explicitly requests it after reviewing the result.

### Step 8: Report the Result

Use this structure:

```markdown
# MongoDB Project Query Optimization Report

## Scope
- Project and affected flow:
- Language, framework, driver/ODM:
- MongoDB version/topology, if known:
- Reproduction command or scenario:

## Baseline
- Correctness expectations:
- Query count and round trips:
- Latency/throughput measurements:
- Explain or trace evidence:

## Findings
### [Priority] [Finding title]
- Evidence:
- Project location and query shape:
- Root cause:
- Proposed change:
- Risks and trade-offs:
- Validation plan:
- Approval status:

## Approved Changes
- Files changed:
- Before/after behavior:
- Before/after performance:
- Tests and checks:

## Index Recommendations
- Proposed index and supporting query shapes:
- Expected read benefit and write/storage cost:
- Artifact-edit approval:
- Live-application approval:

## External Database Findings
- Evidence requiring operator or DBA follow-up:

## Rollback and Remaining Work
- Rollback steps:
- Unvalidated hypotheses:
- Recommended next measurement:
```

## Validation Rules

- Verify result equality, stable ordering, pagination boundaries, authorization filters, tenant isolation, and consistency guarantees.
- Compare the same application scenario before and after the change.
- Prefer distributions and multiple runs over isolated wall-clock samples.
- Separate application latency from database execution, network transfer, and result-processing time.
- State when cache warmth, dataset differences, concurrency, or environment limitations weaken the comparison.
- Never report estimated gains as measured results.

## Limitations

- Static review cannot prove runtime impact.
- Read-only access may not expose every slow query or server condition.
- Index changes affect write cost, storage, build load, and deployment behavior and require operational review.
- Schema redesign, data migration, capacity planning, replication tuning, and sharding changes are outside the default scope.
- Some improvements require a production-like dataset and load profile that the project may not provide.
