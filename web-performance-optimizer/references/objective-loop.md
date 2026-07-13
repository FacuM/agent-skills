# Objective Loop Template

Use this loop for each performance cycle.

## Loop State

- Current target:
- Current score/behavior:
- Bottleneck evidence:
- Risk if changed:
- Files likely affected:

## Cycle Steps

1. Select one bottleneck family only.
2. Write a failing regression or capture baseline evidence.
3. Implement the minimum safe change.
4. Run targeted tests.
5. Rebuild production artifacts.
6. Run post-build export/prerender when applicable.
7. Serve the same artifacts users get.
8. Remeasure.
9. Compare against baseline and previous latest.
10. Decide:
    - keep and continue,
    - adjust and rerun,
    - revert because evidence/UX/freshness regressed,
    - stop because targets are met or blockers are external.

## Stop Conditions

- Target thresholds pass repeatedly enough for confidence.
- Remaining issues are external and documented.
- Next changes would trade correctness/freshness/UX for synthetic score.
- User asked to stop, commit, push, deploy, or hand off.

## Reporting Snippet

```markdown
### Cycle N — [bottleneck]

- Hypothesis:
- Change:
- Validation:
- Measurement:
- Decision:
- Next bottleneck:
```
