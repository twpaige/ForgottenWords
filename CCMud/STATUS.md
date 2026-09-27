---
title: Current status
description: Bounded HOG efficiency implementation, verification and release boundary.
reviewed: 2026-09-27
nav: status
permalink: /CCMud/status.html
---

## Current release candidate

The bounded Step 10 efficiency implementation is committed and pushed as
`0472cce96f168e775616ab3b39c0d121c0fa1ec6` in private Crown-Call. Its subsequent pinned-documentation sync commit
is the final DEV candidate. **DEV has not been automatically deployed; PROD is
untouched.** Use only the exact final revision supplied in the completion report
with the normal `sudo cc-update dev <exact-commit>` gate. Linux DEV pytest,
migrations, activation and health checks remain mandatory; local success does
not establish Linux/live verification.

### Implemented behavior

- Immutable context-scoped regional network and bounded catchment/catalog reuse;
  caller-isolated overlays and copies, with natural filtering before copying.
- Cold assembly outside short publication locks; optional regional discovery
  no longer holds the movement-wide certificate lock. No extra workers or
  database/session ownership changes.
- Ordered current/ahead/neighborhood plans each substep; cache-owned epochs and
  bounded deadline reconciliation preserve failure/rejection/expiry/eviction
  obligations. Steering remains continuous. STOP, arrival, target replacement
  and disconnect discard receipts without orphan subscriptions.
- Previously authorized bounded last-32 failure history and admin RAW diagnostics
  included in the release. History remains in-memory, separate from queue rejection,
  and is lost on restart. The historical 13 failures remain unexplained.

**Preserved:** 500-foot chunks, 25-foot samples, identity/seed/geography, two-mile
guard, conservative certification and water blocking, GROUND, exact-heading travel,
APPROACH, JUMP, STOP, stamina/pace, persistent objects and worker/session safeguards.
The Pioneer Cabin remains unaudited; unknown crossing consequences remain blocked.

### Verification and measurements

Full regression suite: **299 passed, 13 warnings in 368.44s (0:06:08)**. Three fresh-process
concurrency/session + field-worker overlap runs: **12 passed, 1 warning in 54.72s; 12 passed, 1 warning in 54.53s; 12 passed, 1 warning in 54.24s**.
Focused regional/runtime/travel: 78 passed; new isolation/plan lifecycle: 13 passed.
No benchmark ran concurrently with these tests.

Fixed physical coverage: 26 dry / 28 water jobs unchanged. Generation wall time
16.994 → 10.428 s / 18.055 → 10.875 s. Geography hashes match. Already-ready
APPROACH replay: 150 prewarm calls / 1,650 requests → six meaningful reconciliations /
zero requests, with the same endpoint; both generated zero jobs. Cold-catalog
movement regional-lock wait: 1.772 s → about 4 microseconds locally.
SAY/LOOK p95 and heartbeat tails improved in the sampled command run; STOP p95
did not improve. Do not generalize these single local Windows/SQLite measurements
to Linux/PostgreSQL/network or multiplayer guarantees.

Read the [implementation report](https://github.com/twpaige/Crown-Call/blob/0472cce96f168e775616ab3b39c0d121c0fa1ec6/docs/hog-efficiency-release.md), [release evidence](https://github.com/twpaige/Crown-Call/tree/0472cce96f168e775616ab3b39c0d121c0fa1ec6/docs/evidence/hog-efficiency-release) and
[HOG implementation contract](hog.html#bounded-efficiency-implementation-2026-09-27).
The [two completed investigations](evidence.html) remain preserved, not rerun or
replaced by these release measurements. Full-source/test lint retains eight
pre-existing findings in untouched files; changed-file lint and diff checks pass.

## Live-environment evidence

Before this release, Thomas reported DEV base
`e54acc982c1b68450b85e9c428fd0e82daa64d9c`, seed `867359018957601`, and successful
field behavior. This task has no new live symlink/service/health inspection.
Earlier Linux verification at `48bdb3faa660` remains historical evidence only.
No DEV/PROD service, database, release pointer or configuration was changed here.

## Settled design not yet implemented

The [September 27 design continuation](design.html#world-time-travel-and-seasonal-light-2026-09-27)
still sets separate 1.75× world time, 2× normal travel convenience and future
0.25×–2× pressure control, plus smooth seasonal daylight. Current runtime retains
its combined approximately 24× travel factor; a general calendar/dynamic controller
was not added. Pressure must never slow ordinary non-travel gameplay.

## Recommended bounded next work

1. Thomas may deploy the exact final candidate through the Linux DEV gate, then
   verify active revision/service/health and field-test travel, APPROACH, STOP,
   water blocks, grounded JUMP, RAW and responsiveness. Do not bypass failed gates.
2. Retain 500/25. Consider remaining spatial indexing/local-water reuse only from
   measured need; immutable reuse is implemented but does not eliminate CPU/GIL
   contention or guarantee priority/preemption.
3. In a separate authorized release, introduce explicit world-time and travel
   convenience with epoch/continuity and telemetry-unit decisions. Dynamic control
   follows only after pressure telemetry/hysteresis/recovery tests.
4. Separately investigate 100-foot storage/interpolation with independent finer
   certification and coarse-interpolant validation; neither coarse nor adaptive
   sampling is authorized by this release.
5. Later build query-specific perception/relevance filtering and on-demand LOS.
   Keep precise movement hazards and detailed RAW; defer prose.
6. Speculative APPROACH corridors, distributed/home workers and full runtime
   pregeneration remain optional future architecture, not immediate requirements.

Do not combine these separate contracts into one implementation release.
Documentation publication and pinned-reference synchronization do not deploy the game.
