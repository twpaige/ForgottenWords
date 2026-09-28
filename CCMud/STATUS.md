---
title: Current status
description: Waterway Geometry v1 implementation and manual DEV release boundary.
reviewed: 2026-09-27
nav: status
permalink: /CCMud/status.html
---

## Current bounded release

**Waterway Geometry v1 is implemented and locally tested**, private code/evidence
revision `52723969e7103254dc20b543a0bae7da41d72880`. The subsequent pinned-documentation commit is the final
release candidate. Read [contract](hog.html#waterway-geometry-v1-2026-09-27),
[release report](https://github.com/twpaige/Crown-Call/blob/52723969e7103254dc20b543a0bae7da41d72880/docs/waterway-geometry-v1-release.md) and [evidence](https://github.com/twpaige/Crown-Call/tree/52723969e7103254dc20b543a0bae7da41d72880/docs/evidence/waterway-geometry-v1).

Existing centerline/width now supplies exact analytic banks, capped parabolic depth,
deterministic class-based current and flow direction. Ordinary movement wades through
<=3-ft water and stops before greater depth. No swimming, forced drift, stamina/speed
change or broader hydrology simulation. Lakes, springs, wetlands and ravines remain independent.
Identity adds waterway_geometry=1; earlier layers and stable feature IDs are unchanged.

Full suite: **369 passed, 13 warnings in 527.34s (0:08:47)**. Three fresh-process concurrency/session runs:
**12 passed, 1 warning in 55.21s; 12 passed, 1 warning in 55.92s; 12 passed, 1 warning in 55.14s**. Changed source/tests pass Ruff;
55 pre-existing repository findings remain. Local timing evidence and limits are preserved.
The 0.85 factor reproduces existing light_woodland movement policy, not a wetland penalty.

## Environment and next action

This task did not access a live database, inspect active game services, or deploy.
**DEV was not deployed and PROD was untouched.** The latest active runtime revision
is not verified by this task. Publication/push/local tests are not live acceptance.

Thomas should run `sudo cc-update dev <exact-final-commit>` from the completion report,
with the established fresh Linux tests/migrations/health gate. For the live major river,
WALK EAST from [70282734,-31352345] should reach the bank at ~864.07 ft, wade about
4.09 more eastward feet, then stop just before >3-ft depth at [70293151,-31352345].
Check RAW membership/depth/current, stop message, refusal of EAST!, safe WEST retreat,
APPROACH, JUMP certification and unchanged unrelated hazards. Check failures and queue
rejections. Cold preparation remains legitimate; no wholechunk water classification.

## Preserved and deferred work

Keep 500-ft chunks/25-ft sampling, existing movement/time/stamina, bounded prewarm,
immutable regional products, one generation worker, independent sessions and bounded
failure diagnostics. Historical 13 failures remain unidentified. The original Step 10
reports and subsequent ravine/wetland evidence remain durable via [evidence](evidence.html).

Deferred: swimming/drowning, current-induced effects, boats, bridges/ford generation,
seasonal stages/floods, wetland penalties, spring geometry, ravine interiors, prose/LOS,
query architecture, dynamic time/travel scaling and distributed workers.
