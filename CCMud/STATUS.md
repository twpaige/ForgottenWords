---
title: Current status
description: Simple admin wildcards and audited legacy ground seed correction.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Admin OLIST, MLIST, FIND O and FIND M accept `*` for any sequence of characters.
`*` alone explicitly requests all matches. Existing catalog limits, the bounded
20-result FIND pager, retained displayed numbers and current-location JUMP FIND
remain intact. Other pattern characters remain literal. See the
[command contract](commands.html#prototype-discovery-and-loading).

## Ground-object investigation and correction

Thomas supplied a read-only DEV database audit on September 30. Exactly two
outdoor, unparented objects lacked ground attachments: the original weathered
iron lantern and worn woodsman's axe. Both retained their original stable IDs,
quantity one, absolute (0,0,0), chunks (0,0), portability and empty morph schedules.
The axe also retained its tool capability. These were flat-world seed placements;
the later opt-in ground-attachment migration deliberately did not convert them.

FIND correctly reported their horizontal locations. Ordinary perception correctly
rejected their absolute Z=0 against the actual HOG ground elevation. This was
legacy seed data, not a Nearby, targeting, capability, chunk or morph regression.

Migration `20260930_08` deliberately adds zero-offset ordinary ground attachments
only to those exact seed identities/prototypes while all original placement and
state guards still match. It preserves identity and stored coordinates. Moved,
held, ROOM, embedded, changed-quantity, morphed, scheduled, removed or already
attached seeds are skipped. Unrelated absolute placements remain absolute.
No runtime observation repairs data, no perception/access rule is relaxed, and no
missing object is respawned. Rollback requires a reviewed data migration rather
than stripping valid attachments. The read-only audit SQL is retained privately
at `scripts/audit_ground_objects.sql`.

## Verification

Implementation: [cb3ebcd](https://github.com/twpaige/Crown-Call/commit/cb3ebcdd14ef979cdc65e0aa183b98e31eb3110c), committed and pushed to main.

- Admin search/loading and FIND/JUMP FIND: **33 passed**, one existing dependency
  warning, 36.89 seconds. Includes wildcard patterns, literal SQL/pattern characters,
  cold searches, bounded paging, retained numbers, parent locations and re-resolution.
- Legacy placement, Hunting Knife and targeting: **32 passed**, one existing
  warning, 55.16 seconds. Reproduces hidden absolute seeds on real DEV HOG terrain
  and verifies migration guards and normal perception/GET afterward.
- Final migration regression file: **11 passed**, one existing warning, 26.62
  seconds, including an explicit successful APPROACH assertion. Final expanded
  MLIST wildcard assertions also passed in a focused three-test run.
- Changed-file Ruff and diff checks passed. Counts overlap. No full suite ran.

Tests use isolated SQLite and real/synthetic HOG fixtures. The supplied live audit
establishes DEV data state; PostgreSQL migration execution and post-correction
live gameplay have not yet been verified.

## Deployment state and next action

No DEV/PROD deployment, migration or restart was performed. Thomas controls FAST
DEV playtesting and full checkpoints. The normal updater will apply the guarded
seed-data migration when this revision is deployed. Afterward, check LOOK,
APPROACH and GET for the lantern/axe at Origin, plus wildcard searches and FIND
paging/JUMP FIND. No additional database output is needed from the completed audit.

Existing prototype loading, hands, ROOMs, Craft/morph systems and broader gameplay
contracts remain unchanged. Parked Builder/content work remains deferred.
