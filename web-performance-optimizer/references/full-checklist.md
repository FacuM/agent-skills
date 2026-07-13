# Full High-Latency Performance Checklist

## Intake

- [ ] Read repo instructions and command rules.
- [ ] Identify production build, serve, deploy, CDN, API, and image-origin paths.
- [ ] Confirm whether implementation, planning, or evidence-only work is requested.
- [ ] Confirm commit/push/deploy permissions.

## Diagnosis

- [ ] Capture critical request inventory.
- [ ] Identify actual LCP element per profile.
- [ ] Classify public deterministic, volatile public, stable catalog, input-dependent, and private endpoints.
- [ ] Inspect headers: `Cache-Control`, `Set-Cookie`, `Vary`, `ETag`, CDN status.
- [ ] Check repeated request behavior and browser cache behavior.
- [ ] Inspect warmup timing, parallelism, and page/store reuse.
- [ ] Identify image hotspots: direct `<img>`, lazy-only offscreen images, thumbnails, pickers, galleries.

## Measurement

- [ ] Use production-like server/artifacts, not dev server.
- [ ] Preserve baseline before optimization.
- [ ] Add check and compare modes.
- [ ] Include mobile and desktop where possible.
- [ ] Include all available audit categories.
- [ ] Document build -> export/prerender -> serve -> measure order.

## Public cache and data warmup

- [ ] Cache only GET 200 responses.
- [ ] Cache only allowlisted public paths.
- [ ] Exclude `Set-Cookie` responses.
- [ ] Add private/session denylist.
- [ ] Use shared edge TTL and SWR.
- [ ] Keep browser TTL zero for volatile admin-editable JSON.
- [ ] Use positive browser TTL only for stable discovery/catalog data.
- [ ] Warm through stores/loaders reused by destination pages.
- [ ] Dedupe in-flight requests and cache empty arrays.
- [ ] Key cache by endpoint params/variants.
- [ ] Launch independent warmups in parallel.
- [ ] Respect Save-Data/2G while avoiding `visibilityState` as a hard abort.

## LCP, prerender, and critical assets

- [ ] Preload real LCP image in initial HTML.
- [ ] Align `href`, `imagesrcset`, and `imagesizes` to hydrated output.
- [ ] Materialize slow remote LCP image locally/edge only with size/time bounds.
- [ ] Defer noncritical app CSS safely with fallback.
- [ ] Remove noncritical font preloads.
- [ ] Patch built `index.html` after build, not before.
- [ ] Keep markers idempotent.
- [ ] Validate no visual shell flash on cold or repeat load.

## Image placeholder and warmup

- [ ] Direct noncritical images have placeholder/fade/error state.
- [ ] Cached images are handled with `complete` and `naturalWidth`.
- [ ] Known URLs warm via deduped low-priority queue.
- [ ] Warmup is bounded and delayed/idle enough not to compete with LCP.
- [ ] No extra API pages are fetched solely to discover images.
- [ ] Initial and appended offscreen batches start image requests before scroll.

## Evidence

- [ ] Unit/contract tests pass.
- [ ] E2E for changed behavior passes.
- [ ] Build/export succeeds.
- [ ] Performance check passes or blockers are documented.
- [ ] Header/CDN/browser-cache evidence captured.
- [ ] Cold-load video/trace captured for visual first-render changes.
- [ ] Final report includes commands, outputs, scores, files, risks, and rollback notes.
