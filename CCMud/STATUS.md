---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

## Current milestone

HOG Step 10 DEV exploration now has admin RAW facts, bounded regional diagnostics,
continuous eight-direction travel and lazy dry/gentle terrain certification beyond
the initial 2,000-foot administrative boundary. Final prose is not implemented.
Thomas explicitly chose to keep distant geography as admin diagnostics in this
milestone; certified distant LOS and player-visible discovery remain separate.

The starting implementation was private Crown-Call `280aea0e021ba03c0f314698b5efe8e41e69afb5`,
which already enabled real HOG walking. Older status references describing all
walking as disabled were stale. Seed remains `867359018957601`, start `(0,0,GROUND)`,
continuous ground 17156.440005848694 inches and persisted Z=17156. Generator geography
and settled pace, stamina, terrain factors, precise hazards and offline rest are retained.

## Implemented and constrained

- Admin command `hogdisplay raw|prose`, independent of BRIEF, defaults to PROSE on
  reconnect. Authorization is checked before diagnostics are returned. PROSE keeps
  the existing simple underfoot presentation. Browser commands echo before output.
- RAW LOOK/travel report precise ground position, grade magnitude/uphill bearing,
  biome/surface, regional cover, dry-ground/certificate state and cache information.
- Regional discovery searches internal doubling bands from 10 feet to the 50-mile
  search ceiling; at most twelve selected diagnostics show feature class, bearing,
  estimated distance and source prominence where available. No empty bands or
  fabricated landmarks. Unknown visibility stays unknown; player facts exclude it.
- Existing bounded background chunk cache/prewarming now admits additional dry,
  gentle chunks. Unknown/unresolved terrain stops travel. No continent-wide walking
  raster, persistent terrain cache, auto-relocation or consequence-free hazards.
- East/west travel crosses the former edge. Actual north/south candidate chunks
  containing unresolved water/feature data are rejected. The footprint of the
  preserved Pioneer Cabin also blocks the direct north-origin route.
- Eight compass directions use east +X and north +Y, with normalized diagonal
  physical speed and directional grade. No cardinal-axis defect was found. The
  reported corner coordinates could include a prior southward leg; live command
  history was unavailable, so that explanation is not asserted as proven.
- Walking-policy version 2 accepts the exact prior compatible DEV identity after
  current local certification. No database migration or character relocation is added.

## Persistent north-origin structure

The migration-seeded `pioneer_cabin` occupies X=-90..90, Y=1230..1410 inches at
absolute floor Z=0. Its door exterior is `(0,1200,0)`, interior `(0,1260,0)`.
It is legacy persistent test content, not a generated HOG feature. No reviewed
GROUND/footprint/interior/portal adaptation exists, so HOG blocks its XY footprint.
Do not delete, relocate or approve it without Thomas's separate decision. The
migration and implementation establish this diagnosis; live DB rows were not read.

Implementation committed and pushed to private main as `4215ba4785437caafef20da737bb780f1b86e5ea`.

## Verification

Initial expanded full suite: 193 passed, 13 upstream warnings, 263.48 seconds.
Final full suite: 197 passed, 13 upstream warnings, 261.91 seconds; eight additional real-seed/admin-RAW compass tests passed in 30.57 seconds. Changed-file lint passes;
full lint retains the same eight pre-existing findings in untouched generator/tests.
The browser script parses, and a Node check confirms command echo occurs before send.
Benchmarks use real HOG and isolated SQLite, excluding live PostgreSQL/network/player
concurrency. New chunk certification/materialization median is 435 ms (p95 483 ms); warm regional diagnostics 21.2 ms (p95 22.9 ms); SQLite movement tick 1.62 ms (p95 2.41 ms). Cold origin setup took 23.1 seconds; regional catalog preparation took 1.56 seconds. Measurements and exact commands are maintained in private Crown-Call's
`docs/hog-exploration-benchmark.json` and `scripts/benchmark_hog_exploration.py`.

## Environment and next action

Thomas reports successful live DEV HOG walking. This continuation could not connect
to server SSH (port 22 timed out); no DEV migration/deployment or PROD operation was
performed. Use the canonical development guide's `sudo cc-update dev`, compare the
active release to the mirrored revision, check the service and local port-8001
health. Expected exploration health field: `hog_exploration=dry_gentle_chunks_v2`.

Enter DEV using an admin account and a HOG test character. Use `hogdisplay raw`,
`look`, `walk`, `east`; use `stop` to halt. Repeat LOOK if the regional catalog is
still preparing. RAW observations arrive at the existing 15-second cadence while
hazard checks remain continuous. A cold resumed chunk may require retrying entry.
Start a new HOG test character with explicit ATHLX for an origin-based test; existing
characters retain their current positions. Use `hogdisplay prose` to return to the
simple normal presentation.

Next work is certified distant perception/LOS, broader precise terrain geometry,
and a deliberate cabin placement decision if desired. Outdoor object interactions,
swimming/falling consequences, final prose, other zones and unrelated systems remain
outside this milestone. Source publication, pinned-reference synchronization and game
deployment are separate completion states; PROD requires explicit authorization.

