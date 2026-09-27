---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

## Current milestone and verified live DEV

The bounded Step 10 field-testing utility update is implemented, locally tested,
committed and pushed as `84b84616ddab7321a9dec2985d149fd01427a3e8` in private Crown-Call. Its final documentation-sync
revision is the next DEV candidate. **This utility release is not yet deployed or
verified on the Linux server.** The server's complete pytest gate remains required.
PROD is untouched. No migration, seed/generator change or automatic relocation is added.

Thomas reports the previous concurrency correction **`48bdb3faa660` is active on DEV**:
225 tests passed on the actual Linux server, migrations and health passed, and
`/srv/crown-call/dev/current` was manually verified as
`/srv/crown-call/dev/releases/48bdb3faa660`. The native segmentation fault did not
recur. This supersedes the earlier pre-deployment status and the older `6b270d460cb9`
symlink observation. These server results are Thomas's verification, not a new SSH
inspection by this development session.

Thomas's subsequent field tests confirmed continuous multi-chunk travel, background
generation, responsive SAY/STOP, normal exhaustion and pace changes, asynchronous
regional RAW diagnostics, local water exclusions, and zero observed generation
failures/queue rejections. At stream `867359018957601:waterway:7346:593`, ordinary
travel stopped at the inferred boundary while travel along/away from it remained
possible. `east!` was recognized and correctly refused unimplemented consequences.
The new utility regression exercises this same generated stream geometry.

## Completed field utilities

- **CLEAR:** client-only transcript removal, preserving status, input, socket,
  active travel, stamina and all server/HOG state. HELP documents it; no button.
- **`TRAVEL <0-359>`:** whole-number clockwise compass heading. It starts/redirects
  the existing controller at selected pace; malformed headings never start travel.
- **`APPROACH <id|feature_id>`:** admin-only direct diagnostic steering. Ready local
  water polygons take precedence; otherwise the current bounded regional catalog
  supplies a real anchor/centerline/shoreline within 50 miles. No global ID index,
  invented geometry or route planner. Cold catalogs return immediately with a retry
  message. Nearest-point steering updates every substep; arrival stops within two
  inches of the representation. All ordinary movement safety/stamina/STOP rules apply.
- **`JUMP <direction> <positive miles>`:** admin outdoor field placement, with all
  eight full names and N/S/E/W/NE/NW/SE/SW. `JUMP NE 192` is 192 miles total along
  45 degrees. It requests/checks the destination, not the intervening route. Cold or
  rejected destinations do not move the player or cancel travel. Success cancels
  travel/warnings, preserves pace/stamina and saves the destination's **HOG GROUND Z**,
  not the source Z. Certified dry standing ground and persistent footprint checks
  remain mandatory; many far destinations can still be conservatively refused.
- **Status:** plain-text effective game mph and compass heading beside stamina,
  using existing worker messages. Stopped status shows 0 mph and no active heading.

Heading/APPROACH reuse the established certification, prewarming, caching, terrain,
slope, hazards/water, stamina, observation and per-character worker flow. No new
database ownership path is added. Targets contain immutable coordinates; sessions
stay operation/thread-owned and close before integration. See [HOG](hog.html#bounded-field-testing-commands)
for exact semantics, integer-coordinate tolerances and resolution limits.

## Verification

- Focused utility and movement regression run: **90 passed**, 44.61 seconds.
- Final complete suite: **285 passed**, 13 upstream warnings,
  378.43 seconds, with Python fault handling enabled.
- Three fresh-process concurrency runs: **12 passed each**,
  57.3 / 55.36 / 55.35 seconds. Each includes the existing
  connection/session suite, original real-HOG API/socket test and new worker-command
  overlap test (heading, APPROACH, JUMP and STOP alongside direct DB reads).
- New tests cover seven representative headings, invalid input, precise vectors,
  pace/stamina/observations/STOP, inferred stream blocking, point/extended geometry,
  dynamic steering, bounded/unknown/unready targets, all 16 JUMP name/alias forms,
  normalized 192-mile diagonals, destination GROUND Z, rejected placement,
  telemetry, access limits and thread/session boundaries.
- Browser scripts execute shipped client functions: CLEAR sends nothing, preserves
  active state/status, and permits later output/commands; speed/heading rendering
  and automatic-stop presentation pass. Changed-file lint and diff checks pass.
  Full lint retains the same eight pre-existing findings in untouched files.

New command latency in milliseconds (median / p95 / maximum):

| Workload | SAY | LOOK | STOP |
| --- | ---: | ---: | ---: |
| Burst / warm regional | 0.56 / 4.67 / 148.14 | 27.65 / 101.87 / 243.45 | 3.77 / 27.50 / 218.33 |
| Sustained / cold regional | 0.54 / 4.31 / 270.94 | 28.96 / 225.87 / 307.70 | 5.67 / 7.60 / 804.83 |

Nominal-five-ms event-loop heartbeat p95/max: **33.33/88.62 ms**
for burst, **39.22/119.72 ms** for sustained/cold work.
These are the established 30-round workloads on local Windows with independent
SQLite WAL/QueuePool connections, real HOG generation and no competing test run.
They are not Linux/PostgreSQL/network or multiplayer guarantees. Cold scheduling
tails remain; no uniform improvement is claimed. Private
`docs/hog-field-utilities-benchmark.json` retains aggregates, environment and commands.

## Preserved boundaries and next action

`9e70cce` remains a rejected release, not a deployment candidate. Its unsafe SQLite
StaticPool connection sharing was corrected in the active `48bdb3faa660` release.
The exact native crash was not reproduced locally; subsequent Linux success is
recorded above. No session checks, concurrency tests, offloading or background HOG
generation were weakened for these utilities.

Seed `867359018957601`, GROUND walking policy `water_exclusions_v3`, deterministic
geography, caches and persistent content remain unchanged. Unknown water depth,
bank geometry and crossing consequences stay blocked. Distant features remain RAW
diagnostics with `perceived=false`. The Pioneer Cabin remains unaudited. No prose
generator, distant LOS, swimming or unrelated Step 10 work is implemented.

Deploy only the new exact final revision provided with this update using
`sudo cc-update dev <exact-commit>`. Do not bypass the gate. After success, compare
the active symlink basename with the requested commit's first 12 characters and
verify service/health. Then field-test CLEAR during travel, heading 298 along the
stream, APPROACH from RAW IDs, STOP, and admin JUMP with destination GROUND checks.
Verify the simple speed/heading display throughout. This candidate becomes DEV
verified only after Thomas reports the actual deployment/activation checks.
Documentation publication and reference synchronization are separate from game
deployment; PROD is not authorized.
