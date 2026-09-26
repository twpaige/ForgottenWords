---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

This is a current snapshot. Code and executed checks establish implementation state.

## Current phase

**HOG Step 10: gated runtime contracts implemented; walking gameplay deferred.**
Thomas authorized Step 10 on 2026-09-26, then explicitly chose to keep movement gated
pending a separate walking-geometry milestone and confirmed hazards gated pending
consequence handlers. Step 10 as a whole is not complete. Step 9 previews are unchanged.

The private Crown-Call implementation baseline inspected was `25fd3c2` on `main`.
The implemented/tested/pushed contract revision is
`2fa0a6b1924490b0fedc8048306056360ba632e8`. Application package version remains 0.6.0.

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

1. Approve/build deterministic walking geometry with exact cliff, shoreline/channel
   boundaries and water/ground depths. Verify seamless cross-chunk behavior.
2. Measure complete geometry fidelity, movement/LOOK latency, memory, concurrency,
   travel prewarm and cache pressure across terrain types and seeds before tuning.
3. Add regional indexes, conservative LOS/cover/elevation and database/live suppliers.
4. Adopt ground placements in gameplay, object-specific footprint audits and approved
   consequence handlers, with deliberate treatment of legacy placements.
5. Review Step 9 coasts/waterfalls/landforms, choose a seed and hand-place Origin.
6. Deploy/verify the contract revision on DEV when an authenticated server connection
   is available. No browser server session or local SSH configuration was available
   in this task; no live migration or game deployment was performed.

Additional zones, cave connectivity, civilization/transportation, discovery/naming,
weather, final prose and complete skills/perception remain outside this phase.
PROD always requires separate explicit release authorization.

## Verification

- Existing world/terrain baseline: 20 passed.
- Final full contract regression: **117 passed**, 13 upstream deprecation warnings,
  162.98 seconds, on the implementation working state committed as `2fa0a6b`.
- Expanded focused runtime/world coverage: 31 passed, including isolated SQLite
  attachment upgrade/downgrade preserving owner records and scoped command hazards.
- Changed-file lint passed. Full lint reports nine existing findings in untouched
  biomes/elevation/main/worldgen modules and biome/elevation tests.
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
