---
title: Current status
description: Pioneer Cabin as a DWELLING OBJECT with owned coordinate-free ROOMs.
reviewed: 2026-10-01
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

The Pioneer Cabin ROOM-object POC is implemented. The cabin is a DWELLING OBJECT
owning Main Room, Bedroom and Loft. Space/Portal retain reviewed exterior geometry
and door state; they no longer own interior occupancy. PCs, MOBs and objects use
ROOM membership with null indoor coordinates. Held/embedded identity is preserved.

Main Room north ↔ Bedroom south takes one real second. Main Room up ↔ Loft down
takes two seconds. All four links cost zero stamina. Only Main Room can EXIT to
the real exterior doorway. Exact directed links use a small session-local pending
transition, not HOG movement. LOOK/SAY preserve it; conflicting commands cancel it
automatically. Disconnect/restart cancels without cost. Arrival revalidates, commits
membership/stamina exactly once and calls normal LOOK directly.

Same-ROOM perception/handling and speech do not use outdoor range. Other ROOMs'
contents remain inaccessible. Multiple PCs and MOBs can occupy a ROOM. Fixed
infrastructure cannot be ordinarily handled, consumed, morphed or deleted, and
system instantiation gives each dwelling independent ROOM/link identities.
FIND follows nested parents into ROOMs and reports ROOM/dwelling context; JUMP FIND
enters the actual containing ROOM without moving the target. Indoor MOB combat
remains excluded. See the [governing POC contract](objects.html#22-pioneer-cabin-room-object-poc-2026-10-01).

## Persistence and migration

Migration `20261001_01` preserves applicable cabin occupants/object identities,
held/embedded relationships, exterior footprint and door state. Existing cabin
occupants/contents become Main Room members; their obsolete indoor coordinates
are cleared. Unknown exterior Space layouts or conflicting up/down Craft aliases
fail preflight for review. No automatic repair, respawn or deletion of possessions
is introduced. ROOM/dwelling prototypes stay outside the ordinary Object Builder.

The cabin's existing HOG attachment checks remain in force, including reviewed
root/entry ownership. A second system-instantiated layout does not gain automatic
HOG placement approval. There is no ROOM Builder, BUILD CABIN, vehicle, dungeon,
property or indoor combat implementation.

## Verification

Implementation: [b6e70bf](https://github.com/twpaige/Crown-Call/commit/b6e70bf041f66dd6da647308768b9615e8c82cff), committed and pushed to main.

Focused POC and directly affected checks covered:

- migration identity/placement preservation and rejection of altered geometry;
- separate dwelling ownership, room isolation, multiple PCs/MOBs, GET/DROP and restart;
- delayed links, automatic cancellation, disconnect, link edits and concurrent
  completion with one stamina charge;
- infrastructure lifecycle/Craft/morph guards and trigger conflicts;
- real HOG doorway/collision/attachment, nested FIND/JUMP and reconnect;
- actual WebSocket deadline-driven arrival, normal destination LOOK and room speech;
- existing cabin, world, FIND paging/parent chains, outdoor THROW/knife handling,
  indoor Craft/morph and historical migrations.

Successful targeted runs included 13 POC/indoor checks, 20 WebSocket/handling
checks, and 20 lifecycle/legacy-migration checks; counts overlap. The initial
cabin/world/FIND run passed 55 cases; its stale fixture/assertion failures were
corrected and checked separately. Final bounded check: **6 passed** in 3.12 seconds (delay/cancellation, independent ownership, concurrent
arrival, WebSocket deadline, migration), followed by the final indoor-scope check
(**1 passed** in 1.13 seconds).
Changed-file Ruff, whitespace and a single Alembic head were checked. No full
suite was run. Tests use isolated SQLite; PostgreSQL migration execution and live
server behavior remain unverified. The existing Starlette/httpx warning remains.

## Deployment and next action

No DEV/PROD deployment, migration or restart was performed. Thomas controls FAST
DEV playtesting and full checkpoints. After deployment, check ENTER CABIN, north,
south, up, down, cancellation, EXIT and two-player ROOM visibility/speech, then
FIND/JUMP FIND for an indoor object. The updater applies the committed migration.
This migration changes indoor storage; reverting the binary alone cannot restore
the former schema. Database rollback requires a reviewed restoration/data migration.
Use `sudo cc-update dev --fast <commit>` for iteration or
`sudo cc-update dev <commit>` for the deliberate full-verification checkpoint.
