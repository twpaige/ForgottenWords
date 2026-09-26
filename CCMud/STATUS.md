---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

## Current milestone

HOG Step 10 DEV exploration now certifies dry exterior ground around inferred local
channel/lake exclusions, exposes factual water diagnostics in admin RAW, and stops
before unresolved water with an explicit `You stop.` Browser commands still echo
before output. Distant geography remains admin diagnostics; certified LOS/player
discovery and the prose generator are deferred by Thomas's decision.

Private implementation: `834499711d91035ddfdf534dd210b97aa87c5c6d`, committed and pushed to Crown-Call main.
Seed remains `867359018957601`, origin `(0,0,GROUND)` with continuous elevation
17156.440005848694 inches and persisted Z=17156. No character or structure is moved,
no new migration is required, and the physical generator formula is unchanged.
Walking-policy version 3 revalidates exact compatible v1/v2 identities on entry.

## Exploration stop and water boundary

Thomas reports successful extended DEV exploration, dynamic chunk expansion,
15-second RAW observations, ordinary commands during travel, and a safe stop around
1.27 miles east. He also reported a sluggish SAY and missing explicit stop message.
His pasted deployment log verified 205 passing tests and healthy DEV at `6b270d460cb9`.

Strict east/Y=0 reproduction of that revision instead first denied chunk (16,0),
X=96,000 inches (1.51515 miles): stream `867359018957601:waterway:7346:617`, width
17.8886874 feet. The stream's broad bounding rectangle overlapped the chunk while
its represented channel did not. Exact live coordinates/history were unavailable;
the 1.27/1.515-mile discrepancy is not presented as resolved.

Walking now intersects chunks with inferred channel corridors and lake polygons.
Boundaries are independent of the 25-foot elevation samples. Only their dry exterior
is certified; beds, bank slopes, shore profiles, depth and current velocity are not
invented. Unknown/intermittent/dry water footprints remain inaccessible. Other
unresolved coasts, regional macro-lakes, springs, wetlands and natural extents retain
safe refusal. Water confirmation remains gated by the consequence contract.

The first represented crossing on the now-open strict east route is stream
`867359018957601:waterway:7346:593`, width 15.1888570 feet, at
(197096.3648707,0) inches (3.1107381 miles). The regression stops at (197096,0).
RAW identifies the inferred boundary, distance/bearing, polygon, source width,
modeled discharge/seasons, flow bearing, unknown measurements and blocked state.
These diagnostics retain `perceived=false`.

## Command latency and prewarming

Synchronous game/ORM work previously ran on the WebSocket event loop; repeated
database queries occurred across travel substeps. Commands now run in worker threads
with per-character ordering. Each bounded travel update reuses one character/load/
structure snapshot while retaining per-substep terrain/hazard checks. STOP avoids
an obsolete RAW observation. Runtime sampling yields 5 ms between rows; startup does
not add these waits. Prewarming prioritizes two chunks forward (configurable 1–3)
before neighbors, with existing one-worker, 16-pending and 64-chunk bounds.

The comparable isolated SQLite burst benchmark measured p95 milliseconds:

| Command | Before | After |
| --- | ---: | ---: |
| SAY | 51.7 | 4.2 |
| LOOK | 126.5 | 30.5 |
| STOP | 711.6 | 2.3 |

Maximum nominal-5-ms event-loop heartbeat gap fell from 1239.7 to 142.3 ms. The
after-run cold SAY peak was 272.2 ms, worse than the baseline's 128.1 ms; not every
tail measurement improved. A separate sustained-generation/cold-regional stress
run recorded p95 SAY/LOOK/STOP 0.6/80.1/5.1 ms but maxima 137.7/384.4/828.2 ms.
Remaining Python GIL/cold-catalog contention is explicit. These are local Windows,
single-character, SQLite measurements, not live PostgreSQL/network guarantees.
Private `docs/hog-water-benchmark.json` retains measurements, limits and reproduction
commands; scripts also measure actual water materialization/interception.

## Verification and remaining decisions

Full regression: **215 passed**, 13 upstream warnings, 289.20 seconds.
Coverage includes real-seed water approach/chunk seams/RAW, sub-grid exclusions,
unknown-water refusal and confirmation, all eight directions, stamina/offline rest,
persistent structures, worker ordering, STOP during held generation and bounded
database work across catch-up movement. Changed-file lint and browser syntax/stop
presentation checks pass. Full lint retains eight pre-existing findings in untouched
generator/tests. The focused development run also passed 61 tests.

Pioneer Cabin remains preserved and unaudited: footprint X=-90..90, Y=1230..1410
inches, absolute floor Z=0; its door exterior is (0,1200,0). Its XY footprint blocks
HOG travel pending a deliberate placement/portal audit. No live DB rows were read.
Deferred decisions are physical bank/bed/depth and current models, swimming/falling
consequences, cabin placement and certified distant LOS. Safe boundaries remain in
place; none requires suspending the completed independent work.

## DEV deployment and next live test

Current-phase deployment is **not performed or verified**: authenticated SSH access
could not be established because port 22 timed out. PROD was not touched. Deploy the
final pinned-reference commit supplied with this phase using the documented
`sudo cc-update dev <commit>`, compare its first 12 characters with the active release,
check `crown-call-dev` and local port-8001 health. Expected health:
`hog_exploration=water_exclusions_v3`. The tracked updater allows 90 DEV readiness
attempts for startup certification.

Use an admin HOG test character, `hogdisplay raw`, `look`, `walk`, then `east`.
Existing characters retain their positions; create a separate explicit-ATHLX test
character for an origin-based reproduction. Issue SAY, LOOK, STOP and direction
changes while generation is active. Continue east to the water warning, confirm
`You stop.`, and inspect RAW width/unknown depth/boundary facts. `east!` must remain
blocked; `west` returns toward known dry ground. Capture exact coordinates and the
echoed transcript for any unexpected stop or lag. Cold certification may require
retrying entry/LOOK; do not bypass an unresolved safety refusal.

Documentation publication, private pin synchronization and game deployment remain
separate verification states. Broader Step 10 is still incomplete.
