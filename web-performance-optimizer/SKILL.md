---
name: web-performance-optimizer
description: Use when a public web page or application is slow because of high-latency APIs, cache or CDN behavior, LCP regressions, fragile prerendering, inefficient asset delivery, or image-loading bottlenecks.
---

# Web Performance Optimizer

## Purpose

Run the complete optimization workflow for public pages affected by a distant backend, high-latency API origin, slow image CDN, or fragile first render. Combine diagnosis, production-like measurement, safe backend/origin-hit reduction, LCP/asset optimization, image UX warmup, and evidence handoff.

## Operating Principles

- Start with root cause. Do not edit code before mapping production shape, critical requests, endpoint classes, and measurement strategy.
- Measure production-like artifacts. Avoid dev-server evidence for LCP, cache, prerender, or CDN conclusions.
- Reduce origin hits safely. Prefer short shared edge cache plus store-level warmup over long browser JSON cache for volatile data.
- Keep private and user-dependent data out of shared cache and blind warmups.
- Optimize perceived speed without visual regressions. Reject static shells, delayed modules, and eager image loading if they worsen real UX.
- Work in small cycles. Each cycle should change one bottleneck family, rebuild/export if needed, run targeted tests, measure, and decide whether to continue.

## Full Workflow

### Phase 0 — Repo and constraint intake

Read local instructions such as `AGENTS.md`, `CLAUDE.md`, package scripts, deploy docs, Docker/Nginx configs, and CI workflows. Capture constraints around command prefixes, sandboxing, secrets, commit/push/deploy rules, and required proof style. If the user requested implementation, proceed with conservative assumptions; otherwise produce diagnosis and plan first.

### Phase 1 — Diagnosis

Identify the production serving model: SPA, SSR, SSG, prerender/export, CDN/edge, reverse proxy, API origin, image origin, and final production-like audit URL. Build a critical request table covering document, JS, CSS, fonts, LCP image, initial public APIs, delayed warmups, route chunks, and post-home public navigation targets.

Classify each API and asset family:

- public deterministic GET suitable for warmup/cache,
- volatile admin-editable public JSON,
- stable catalog/discovery data,
- input-dependent public requests,
- private/session/personalized/admin/checkout/account data,
- LCP-critical image/assets,
- noncritical but likely-to-scroll image assets already known to the frontend.

Inspect `Cache-Control`, `Set-Cookie`, `Vary`, CDN status, browser disk-cache behavior, and repeated-request behavior. Check whether warmups are sequential, hidden behind `visibilityState`, delayed too late for inspection, or not reused by destination pages.

### Phase 2 — Measurement harness

Create or reuse baseline/check/compare scripts against production-like served artifacts. Preserve the pre-optimization baseline. Use mobile and desktop profiles when available. Suggested targets: mobile performance >=95 and accessibility, best-practices, SEO, plus extra available audit categories at 100 unless blocked by documented external factors.

Run build -> post-build export/prerender -> serve -> measure in that order. Document any command that overwrites `index.html`, generated preloads, static snapshots, or local LCP image artifacts.

### Phase 3 — Implementation cycles

Run cycles in this order unless diagnosis points elsewhere:

1. **Public edge cache and data warmup.** Add explicit public GET allowlists, deny private/session overlap, skip `Set-Cookie`, use short `s-maxage` plus `stale-while-revalidate`, keep browser `max-age=0` for volatile JSON, and use positive browser TTL only for stable catalog/discovery data. Warm deterministic public data through stores/loaders with in-flight dedupe, short memory TTL, cached empty arrays, per-variant keys, parallel `allSettled`, Save-Data/2G guards, and no hard `visibilityState` abort.
2. **LCP and critical assets.** Confirm actual LCP. Make the chosen image discoverable in initial HTML with matching `preload`, `fetchpriority`, `srcset`, and `sizes`. If a remote image origin causes latency, materialize a bounded local/edge variant during build/export. Defer noncritical CSS/fonts/JS only when UX-safe. Patch built HTML after build via stable markers.
3. **Prerender/static shell safety.** Use content snapshots or static shells only when they do not create stale/intermediate UI. Validate cold and repeat loads. If a visual shell causes shell -> loader -> app flicker, keep useful markers/preloads but remove visual markup and artificial module delays.
4. **Nonblocking image warmup.** Replace direct noncritical `<img>` hotspots with placeholder/fade components. Add a deduped low-priority `new Image()` warmup queue for URLs already present in frontend data. Keep DOM lazy, avoid extra API discovery, bound concurrency, and handle cached images with `complete/naturalWidth`.
5. **Polish only after bottlenecks.** Tune route chunk warmup, font preloads, motion/fades, and placeholder thresholds only when measurements or recordings prove they matter.

After each cycle, run targeted tests, rebuild/export if required, remeasure, update the comparison, and decide whether the next bottleneck is worth another cycle.

### Phase 4 — Objective loop

Maintain an explicit loop:

1. State current objective and score/behavior target.
2. Identify the single highest-impact bottleneck with evidence.
3. Add or update a failing regression where practical.
4. Implement the smallest safe change.
5. Run targeted validation.
6. Rebuild/export/serve production-like artifacts.
7. Measure and compare to baseline/latest.
8. Keep, adjust, or revert the change based on evidence.
9. Stop when targets pass, remaining blockers are external/documented, or further changes risk UX/freshness.

### Phase 5 — Evidence and handoff

Finish with reproducible proof: commands and results, baseline vs final table, headers/CDN evidence, store/cache reuse evidence, private non-cache proof, image warmup-before-scroll proof when relevant, cold-load videos/traces for visual changes, risks, rollback/TTL notes, and git status. Do not commit, push, or deploy unless explicitly requested.

## Required Checklist

Before claiming completion, verify:

- production-like measurement was used,
- baseline was preserved,
- private/session/cookie responses are not public cached,
- volatile JSON does not get long browser TTL,
- public warmups are reused by destination pages,
- slow warmups do not block independent warmups,
- LCP preloads match hydrated image selection,
- build/export order is documented,
- no visual static shell flash remains,
- known offscreen images can warm before scroll when image work changed,
- tests and evidence cover changed behavior,
- remaining blockers are concrete and dated.

## Additional Resources

- `references/full-checklist.md` — detailed all-phase checklist.
- `references/objective-loop.md` — loop template for iterative goal execution.
- `references/evidence-template.md` — final report template.
