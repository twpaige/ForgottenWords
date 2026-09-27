---
title: Current status
description: Wetland Footprint v1 implementation and manual DEV release boundary.
reviewed: 2026-09-27
nav: status
permalink: /CCMud/status.html
---

## Current bounded release

**Wetland Footprint v1 is implemented and locally tested**, at private code/evidence
revision `3812799921c4fa5ecfaef070fd82fcaf36d861d9`. The subsequent pinned-documentation commit is the final
release candidate. Read the [contract](hog.html#wetland-footprint-v1-2026-09-27),
[report](https://github.com/twpaige/Crown-Call/blob/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/wetland-footprint-v1-release.md) and [evidence](https://github.com/twpaige/Crown-Call/tree/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/evidence/wetland-footprint-v1).

Ordinary wetlands are traversable deterministic ellipses with exact membership,
gross-area preservation and stable IDs. The previous whole-guard wetland refusal
is removed. Biome, water, springs, ravines, terrain and placement checks remain
independent. Wetland RAW records are compact; no movement or stamina penalty is added. Identity adds
wetland_footprint=1 while dev_walking=4 and ravine_reservation=1 remain unchanged.
Historical identity occupants revalidate without automatic relocation for marsh.

The live outside destination [69363083,-31266049], chunk [11560,-5212], certifies
locally. Rounded live-marsh anchor [69588139,-31352317] also certifies with membership.
The complete suite includes the exact real-game JUMP, RAW and interior regression.

Full suite: **345 passed, 13 warnings in 506.53s (0:08:26)**. Three fresh-process concurrency/session/API runs:
**12 passed, 1 warning in 56.17s; 12 passed, 1 warning in 55.24s; 12 passed, 1 warning in 55.50s**. Changed files pass Ruff; 55 pre-existing repository lint
findings remain. Node syntax and actual map-selection assertions pass. Local
measurements, warning details and limitations are in the release report.

## Environment and next action

Thomas identified prior DEV revision 421f6dcc4af797c2d5463975a7ee3de2f3fdf8fe.
This task did not inspect live service/health, access a live database or deploy.
**DEV was not automatically deployed; PROD was untouched.** Local tests are not
Linux or live acceptance evidence. The deployment gate is unchanged.

Thomas should use `sudo cc-update dev <exact-final-commit>` from the completion
report. Require fresh Linux pytest and migrations before activation, then health
checks with existing rollback/safe-current behavior. After deployment, repeat
`JUMP SOUTH 500` from the reported region and RAW LOOK. Check destination membership,
generation identity, failures/queue rejection and unchanged water/ravine behavior.
Also verify an interior point in the same marsh. Cold preparation remains legitimate;
ordinary wetland proximity or membership must not cause refusal.

## Preserved and deferred work

Retain 500-foot chunks/25-foot samples, current movement/time/stamina, bounded
prewarm reconciliation, immutable regional products, single-worker ownership,
independent sessions and bounded failure diagnostics. Historical 13 generation
failures remain unidentified; do not retroactively assign a cause.

The [evidence index](evidence.html) preserves the original efficiency/sampling
reports, ravine study/release, and full wetland study plus separate implementation
measurements. The approved simple ellipse supersedes the wetland study's more
elaborate proposal. Do not restart that study as an implementation prerequisite.

Deferred: wetland speed/stamina/mount/wagon effects, dangerous mud or deep pools,
seasonal states, spring geometry, ravine interiors, prose/LOS, query architecture,
time/travel controller, distributed workers and broader generation redesign.
Documentation publication does not deploy the game or authorize PROD.
