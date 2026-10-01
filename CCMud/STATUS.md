---
title: Current status
description: GUI ROOM-template authoring with persistent drafts and explicit validation for use.
reviewed: 2026-10-01
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Object Builder now offers **Dwelling prototypes** alongside ordinary prototypes
and existing ROOM/dwelling instance forms. A reusable definition contains appearance,
5–100-foot rectangular dimensions, one south entrance, entry local key and ROOM
graph. Interior Rooms now provides Add/Edit/Remove, bulk blank-room creation and
compact ROOM/link/door forms with named destination dropdowns. Text import/export
is optional and uses the same template. Template edits affect future instances only.

Save draft persists incomplete rooms, descriptions, links and missing entry for later
sessions. Validate for Use runs the existing complete server validator; Save usable
prototype revalidates and persists usability. Drafts cannot LOAD. Neither form edits
nor template Save creates live objects. The main editor and room dialog fit 1280×720.

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

Migration `20261001_04` adds saved Draft/Usable status and preserves existing validated
templates as usable. It changes no live instance state.

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

GUI/draft implementation: [8f9b559](https://github.com/twpaige/Crown-Call/commit/8f9b559ace0c214546c71b9cfbf3dea08222591a), committed and pushed to main.

Focused existing dwelling tests: **12 passed**. Draft/API, migration and affected
ROOM Builder regressions: **30 passed** in 9.70 seconds. The strengthened final
four-room save/reopen/validate/LOAD and round-trip tests: **2 passed** in 2.02 seconds.
Counts overlap. Coverage includes incomplete-draft LOAD refusal, server validation
on usable Save, revision conflicts, four independent instantiated ROOMs, changing
a template back to draft without affecting an existing dwelling, and preserving
previous usable templates during migration.

Three shipped-HTML browser harnesses pass: the full GUI-only four-room workflow,
existing ROOM form/import behavior and existing Craft/Object forms. The new browser
check covers bulk creation, reopen, room/link/door editing, named destinations, entry,
Validate for Use, draft/usable Save, text import/export, removal/cancel, revision
errors and compact 1280×720 layout. Changed-file Ruff, whitespace checks and single
Alembic head pass. **No full suite run.**

Tests use isolated SQLite and mocked browser HTTP responses with separate Python
API tests. PostgreSQL migration execution and live DEV behavior remain unverified.
Existing Starlette/httpx deprecation warning remains. The previous reusable dwelling
runtime is preserved; no HOG placement, travel, door/key/bar or occupancy redesign.

## Deployment and next action

No DEV/PROD deployment, live migration or restart performed. Thomas controls FAST DEV
and full verification checkpoints. After deployment, create a dwelling prototype,
use Add Rooms → Create, Save draft and reopen it. Edit the rooms/links, select entry,
Validate for Use and Save, then LOAD it. Text import is not required. Existing usable
templates remain loadable and existing dwelling instances retain their state.
