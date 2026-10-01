---
title: Current status
description: ROOM/DWELLING web authoring with transactional ROOM text import/export.
reviewed: 2026-10-01
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

The existing Object Builder now provides Normal, Rooms and Dwellings filters.
ROOM description/appearance and directed links, dwelling appearance/entry, and
owned ROOM lists use a shared validated server authoring service. Existing
Space/Portal geometry and door state remain authoritative and read-only here.
The Pioneer Cabin's Main Room, Bedroom and Loft are available after migration.

Paste/upload → Validate & review → Import uses human-readable ROOM text with local
keys and no database UUIDs. Export saved → Download text supports round trips.
Imports merge keys, preserve matched ROOM identities, replace supplied outgoing
links and retain omitted ROOMs. A dwelling-wide revision and one transaction prevent
stale/concurrent overwrites and partial graphs. Saving new ROOMs explicitly creates
fixed infrastructure; ordinary prototype Save still does not spawn instances.

The proven ROOM runtime is unchanged: coordinate-free occupancy, delayed directed
links, cancellation/revalidation, same-ROOM interaction, nested FIND/JUMP FIND, and
existing HOG doorway/collision rules. See [the authoring contract and text syntax](objects.html#23-roomdwelling-builder-and-room-text-2026-10-01).

## Persistence and boundaries

Migration `20261001_02` adds/backfills local ROOM keys and dwelling author revision/
last editor. It preserves existing occupants, object/link IDs, ownership, exterior
geometry and door state. The earlier `20261001_01` indoor-storage conversion still
applies when upgrading from older code.

New dwelling infrastructure needs an existing unassociated Space with one door;
this authoring slice does not construct/certify new exterior geography. Existing
ROOM keys/ownership and infrastructure weight are fixed. No ROOM/dwelling deletion,
ordinary handling/Craft/morph of infrastructure, BUILD CABIN, general cloning,
Object/MOB text templates, indoor combat, property or publishing system was added.

## Verification

Implementation: [08cb570](https://github.com/twpaige/Crown-Call/commit/08cb5709aacedbae2e26a62588a64af9ca626578), committed and pushed to main.

Focused authoring checks cover load/save/auth/revisions, infrastructure restrictions,
link editing/validation, description changes through LOOK, transactional rollback,
concurrent imports, local keys, export/reimport, restart, separate dwelling ownership,
shared-prototype edit isolation, and migration identity preservation.

The affected regression run passed **37 tests** in 80.16 seconds: authoring, the ROOM
POC/WebSocket arrival, prior ROOM migration, nested FIND/JUMP FIND and existing
Craft/Object Builder API behavior. Final authoring refinements passed **26 tests**
plus the shared-prototype check (**1 passed**) after correcting its missing test
import. Counts overlap. Both shipped-HTML browser harnesses passed, including
existing Craft/Object forms and new ROOM/DWELLING form/import behavior at 1280×720.
Changed-file Ruff, diff checks and single Alembic head checked. No full suite.

Tests use isolated SQLite and mocked browser API responses; the server service/API
is exercised separately. PostgreSQL migration execution and live DEV behavior are
not verified. Existing Starlette/httpx deprecation warning remains.

## Deployment and next action

No game deployment, live migration or restart performed. Thomas controls FAST DEV
and full verification checkpoints. After deployment, open Builder → Objects → Rooms,
edit a cabin description/link, verify LOOK/delay/cancellation and entry/EXIT, then
export, validate and reimport. Try a stale second editor and a malformed template.
Imported new ROOM keys add permanent infrastructure; omitted keys are not deletion.

Use `sudo cc-update dev --fast <commit>` for iteration and
`sudo cc-update dev <commit>` for the deliberate complete-test checkpoint.
Binary-only rollback does not undo the earlier indoor-storage migration; any
schema/data rollback requires a reviewed restoration/migration procedure.
