---
title: Current status
description: Reusable DWELLING prototypes, atomic LOAD and shared doorway state.
reviewed: 2026-10-01
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Object Builder now offers **Dwelling prototypes** alongside ordinary prototypes
and existing ROOM/dwelling instance forms. A reusable definition contains appearance,
5–100-foot rectangular dimensions, one south entrance, entry local key and ROOM text
graph. Draft validation/application creates nothing; Save stores the prototype.
Template edits affect future instances only.

Admin `LOAD O <dwelling_prototype>` outdoors transactionally creates independent
root/ROOM objects, links, door states, entry, Space/Portal geometry and virtual key
grants. Certified dry, level ground and a clear footprint are required. APPROACH
uses existing continuous travel to 15-foot entrance range; ENTER/EXIT use the
instance's entry ROOM and actual doorway. Original Pioneer Cabin geometry remains.

Explicit local door keys pair independently authored link sides. OPEN/CLOSED/LOCKED
plus independent BARRED state is shared; KEY/BAR access is per side. The loading
admin character receives configured instance-scoped virtual keys. Closed doors
prevent departure and cancel delayed arrival without charging stamina. No physical
keys, automatic reverse links or property system.

See [the template and door contract](objects.html#24-reusable-dwelling-templates-and-installed-doors-2026-10-01)
and [commands](commands.html#reusable-dwellings-and-doors).

## Persistence and boundaries

Migration `20261001_03` adds template storage, Space origin/placement identity,
shared door state and side controls, and persistent key grants. It preserves legacy
Pioneer Cabin door state, ROOM identities and occupants. Existing command collisions
with LOCK/UNLOCK/BAR/UNBAR are rejected for review before schema changes. Migration
has no destructive automatic downgrade.

ROOMs remain fixed infrastructure with same-ROOM occupancy and the existing pending
travel model. No BUILD CABIN, multiple entrances, rotation/foundations, template
upgrades, general Object/MOB import, indoor combat, door damage, windows, vehicles,
property or deployment/publishing framework was added.

## Verification

Implementation: [7d8214a](https://github.com/twpaige/Crown-Call/commit/7d8214afa73e527247279cae0444bce98f1a533a), committed and pushed to main.

Focused dwelling and admin LOAD tests: **27 passed** in 9.14 seconds. Coverage includes
prototype validation/revisions/authorization, two independent graphs, asymmetric
shared doors, loader keys, restart persistence, failed-creation rollback, unsafe
placement, actual APPROACH ticks, entry/exit and legacy-preserving migration.

Direct ROOM/instance Builder/HOG cabin regressions were run; the indoor door lookup
was corrected to honor coordinate-free ROOM occupancy. Nearby cabin expectations
now use its authored name instead of a hard-coded label. Failed cases were rerun
successfully. Final targeted dwelling/entry, nested FIND/JUMP FIND, HOG structure,
regional APPROACH and help checks: **6 passed** in 20.24 seconds. Test counts overlap.

Three shipped-HTML browser harnesses pass: existing Craft/Object forms, ROOM form/
import behavior, and the new dwelling prototype draft/review/save interface. The
prototype editor fits 1280×720 without page scrolling. Changed-file Ruff, whitespace
checks and the single Alembic head pass. **No full suite run.**

Tests use isolated SQLite, synthetic HOG ground plus existing real-seed cabin
regressions, and mocked browser HTTP responses with separate Python API tests.
PostgreSQL migration execution and live DEV behavior are not verified. Existing
Starlette/httpx deprecation warning remains.

## Deployment and next action

No DEV/PROD deployment, live migration or restart performed. Thomas controls FAST DEV
and full verification checkpoints. After deployment, create a dwelling prototype,
LOAD it at two clear level outdoor sites, and playtest entrance/ROOM door controls.
The loader receives that instance's configured keys. Use FULL DEV for a milestone
checkpoint after focused live testing.
