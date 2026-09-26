---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

This is a current snapshot. Code and executed checks establish implementation state.

## Current phase

**HOG Step 10: terrain policy, precise transitions and offline recovery implemented;
actual HOG walking gameplay remains gated.**
Thomas authorized Step 10 on 2026-09-26, then explicitly chose to keep movement gated
pending a separate walking-geometry milestone and confirmed hazards gated pending
consequence handlers. Step 10 as a whole is not complete. Step 9 previews are unchanged.

Thomas then resolved the pace/stamina rules in the 2026-09-26 design addendum.
Continuous-travel integration now exists through the existing world service and
WebSocket loop, with persistent pace/stamina, independent observation/safety timers,
scoped game-side consequence handoff and elapsed-time motion. Previously there was
only fixed-distance command movement. The application factory still leaves the new
controller disabled pending certified geometry and supplied game-rule/perception inputs.

The next bounded continuation now implements the configurable terrain/uphill tables,
no downhill bonuses, safe significant boundary reclassification, and timestamp-based
offline resting recovery. Selected pace and stamina rules remain intact. The old
on-foot factor-below-0.5 impassability cutoff is superseded. No local geometry model
or dangerous-slope/drop/water classification thresholds were invented.

The private Crown-Call implementation baseline inspected was `25fd3c2` on `main`.
The initial contract revision was `2fa0a6b1924490b0fedc8048306056360ba632e8`.
The implemented/tested/pushed continuous-travel continuation is
`d04a7321ebcf42da10caa8b28835be49a47565c0`. Application package version remains 0.6.0.
The terrain/offline continuation implementation is
`d1911ee78455b6086da026bd569e73e405600925`; deterministic fixture correction and
final verification revision: `f938385cfd3cc4090bc78d762d94068adce999ac`.

## Implemented contract phase

- Generation identity includes seed, zone, configuration and generator dependencies.
- Configurable full sample chunks, bounded background single-flight generation,
  nonblocking queries, idle/LRU eviction, neighbor prewarm and timing/cache counters.
- Exact segment/boundary tests, crossed-chunk checks, safe failure on missing or
  uncertified terrain, and scoped/revalidated one-transition hazard confirmation.
- Outward regional-query and visibility callback contracts, structured fact layers,
  unknown-LOS exclusion and six-item live people/object summary thresholds.
- Optional WorldService geography seam, gated outdoor LOOK/interactions and movement,
  including exterior exits. Confirmed crossings remain blocked without consequences.
- Additive persistent ground attachments for objects/Spaces, ground-relative resolver,
  database adapter and read-only compatibility reports; no automatic relocation.
- Reproducible candidate-resolution benchmark and regression coverage.

Existing signed inch coordinates, object/Space ownership, independent database
spatial-index chunks and HOG regional generation are reused. No reviewed generator
output changed. See [HOG](hog.html#step-10-mud-integration-and-persistence) for all
accepted requirements, implementation limits, measurements and next phases.

## Known limits

The regional sampler always marks chunks incomplete for walking. Fine sampling of
35–65-mile input grids cannot establish cliff lips, carved channels or water depths.
The application does not enable the new optional geography seam automatically;
legacy gameplay remains the default and is not HOG-backed gameplay. Injected HOG
mode exposes no outdoor facts without certified perception and blocks unknown travel.

GROUND attachments are opt-in. The migration does not reinterpret any existing z
coordinate. Gameplay get/drop/interior/portal height adoption remains future work.
Unknown placement rules or geometry require review; non-point footprint evaluation
also remains review-required. The current audit does not approve a cabin by sampling
only its center/corners.

Runtime sampling trial defaults are 500-foot chunks, 25-foot samples, 64 cached chunks,
300 idle seconds and 16 pending requests. These are configurable development values;
**no final gameplay-resolution or production cache-lifetime default is selected**.
No disk/database derived geography cache is enabled.

## Next bounded milestones

The smallest next phase is **one certified HOG-derived DEV walking area**, not
activation of the existing regional sampler. Before Thomas can log in and walk:

1. Review/build deterministic local geometry for a bounded area, with effective
   terrain classes, directional grades, exact significant boundaries and ground
   heights. Agree physical hazard thresholds; certify seamless neighboring chunks.
2. Wire real character/rule inputs, safe spawn, ground height and basic structured
   LOOK/travel facts into the existing controller and browser. Gate it with explicit
   DEV configuration. Keep unknown areas and unhandled dangerous crossings blocked.
3. Apply additive migrations and verify login, continuous movement, terrain changes,
   seams, stamina, hazards and offline recovery on DEV using PostgreSQL/browser;
   measure real latency/cache pressure. Full falling/swimming consequences, final
   prose and continent-wide perception are not prerequisites for safe-land walking.
4. Deploy/verify the contract revision on DEV when an authenticated server connection
   is available. No browser server session or local SSH configuration was available
   in this task; no live migration or game deployment was performed.

Broader milestones remain regional perception/LOS indexes, object-specific ground
placement/footprint audits, consequence consumers, and final seed/Origin review.

Additional zones, cave connectivity, civilization/transportation, discovery/naming,
weather, final prose and complete skills/perception remain outside this phase.
PROD always requires separate explicit release authorization.

## Verification

- Terrain/offline phase at `f938385`: **173 passed**, 13 upstream deprecation
  warnings, 167.45 seconds. Changed-file lint passes; the eight pre-existing
  repository-wide lint findings remain in untouched generator modules/tests.
  Implementation and fixture correction are committed and pushed to private main.
- New coverage verifies every configured surface and uphill band, unchanged pace/
  stamina, directional downhill behavior, exact and tick-endpoint transitions,
  neighboring hazards and chunk seams. Recovery coverage exercises rapid repeated
  reconnects, persisted elapsed time, caps, backwards clocks, legacy timestamps,
  online WALK accounting, independent rest inputs and additive SQLite migration.
- New warm benchmark medians: ordinary controller step 15.7 microseconds, precise
  safe-edge step 37.3 microseconds, terrain/grade calculation 0.8 microseconds,
  offline calculation 1.5 microseconds. Database/network and real geometry excluded.
- Previous continuous-travel continuation: **138 passed**, 13 upstream deprecation warnings,
  158.90 seconds. Its 21 added tests include persistence, SQLite migration, exact
  endurance, segment hazards, confirmations, cabin walls and automatic WebSocket
  observations/disconnect. Changed-file lint passes.
- Synthetic warm query benchmark: safe segment 5.3 microseconds median, hazard
  segment 6.6 microseconds, 100-ms controller integration 13.3 microseconds.
  This excludes database/network costs and complete natural geometry generation.
- No live DEV migration/deployment or PROD change was performed. The selected-pace
  migration was tested on isolated SQLite; existing coordinates are preserved.
- Terrain-factor and offline-recovery policy are resolved. The remaining consequential
  design boundary is precise deterministic local geometry and physical hazard
  classification; do not reopen the settled movement rules.

- Existing world/terrain baseline: 20 passed.
- Final full contract regression: **117 passed**, 13 upstream deprecation warnings,
  162.98 seconds, on the implementation working state committed as `2fa0a6b`.
- Expanded focused runtime/world coverage: 31 passed, including isolated SQLite
  attachment upgrade/downgrade preserving owner records and scoped command hazards.
- Changed-file lint passed. Full lint now reports eight existing findings in untouched
  biomes/elevation/worldgen modules and biome/elevation tests; main's import order
  was corrected while integrating the scheduler.
- Benchmark: Windows 11, Python 3.12.14, seed 103, existing context setup 5.89 seconds.
  500-foot chunks at 5/10/25-foot spacing materialize in median 39.84/10.11/1.71 ms;
  warm cache plus interpolation is about 7 microseconds. Full measurements and
  qualifications are in HOG and Crown-Call's `docs/hog-runtime-benchmark.json`.
- These are local development results. No PostgreSQL migration or live DEV test
  of the new contract revision has been claimed.

## Environment evidence

| Environment | Last verified state |
| --- | --- |
| Documentation | ForgottenWords Markdown is authoritative; Pages publication and private reference synchronization are completed separately |
| DEV game | Prior revision `e85313185372`, deployed/verified 2026-09-26: 101 tests, migrations, active service and ready health endpoint; not redeployed in this phase |
| PROD game | Prior recorded revision `96ce97abe6f1`; not changed or revalidated; no release authorized |

The previous DEV deployment used the tracked `ops/cc-update`; installed/tracked SHA-256
was `6d6ea347199d5df97604f15a75c1bdaa256970c63be5fad93c539381cb1f03f1`.
`WorldFramework` still carries a historical Step 3 label; actual modules/tests establish
preview scope. Tabletop crossover cleanup/adoption is a separate task.

## Continuation

Publish the canonical ForgottenWords documentation, then refresh Crown-Call references
at that exact committed revision using `scripts/sync_knowledge_base.py`. Preserve the
full source manifest. Commits, pushes, Pages publication and DEV/PROD deployment are
separate evidence states. The next implementation task is walking geometry, not
turning on the regional sampler as playable terrain.
