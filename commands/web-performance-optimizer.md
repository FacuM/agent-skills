---
description: Run the full web performance workflow
argument-hint: [target-page-or-url] [context]
---

Use the **web-performance-optimizer** skill to run the complete diagnosis, implementation, validation, objective-loop, and handoff workflow for `$1`.

Context from the invocation: `$ARGUMENTS`

1. Read repository instructions and deployment constraints first.
2. Map the production serving model, critical requests, endpoint classes, cache headers, LCP, prerender behavior, and image hotspots.
3. Create or reuse a production-like baseline/check/compare harness and preserve the baseline.
4. Run iterative objective cycles: choose one evidence-backed bottleneck, add regression proof where practical, implement the smallest safe change, test, rebuild or export, remeasure, and decide whether to keep, adjust, revert, or stop.
5. Cover the relevant optimization families: public edge cache and store warmup, LCP and critical assets, prerender safety, nonblocking image placeholders and known-image warmup, and reproducible measurement.
6. Finish with commands and results, baseline versus final metrics, cache and privacy proof, UX evidence, risks, rollback or TTL notes, and git status.

Do not commit, push, deploy, or perform destructive actions unless explicitly requested. Prefer correctness, freshness, and real user experience over a synthetic score.
