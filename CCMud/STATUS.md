---
title: Current status
description: Compact Craft/Object Builders and persistent linear timed morphs.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

The authorized Object Builder and timed-morph increment is implemented. `/builder`
now offers **Crafts | Objects**, using compact desktop forms checked at 1280×720
and 1366×768 without ordinary page scrolling. The account screen links to Builder.
Object authoring supports identity, descriptions/keywords, weight, portable,
cutting/woodcutting tools, existing throwing modifiers and timed morph. Authenticated
server validation, revision checks and editor metadata remain authoritative. Saving
never spawns an instance; prototype deletion, wearable/container/armor/durability
fields and MOB/CCAI implementation remain excluded.

A Craft may now produce a non-portable object on the ground at the character's
feet; GET still rejects non-portable objects. Inputs remain portable and tools
remain held. Existing GATHER FIREWOOD and MAKE SPIT definitions retain their sector,
quantity, weight and five-minute Work Timer rules. There is no new campfire recipe,
ignition, fuel consumption or cooking behavior.

Timed morphs use positive real-world integer seconds and acyclic chains of at
most 16 transitions. Creation captures absolute pending deadlines and target IDs;
subsequent deadlines derive from the preceding deadline. Existing schedules are
unaffected by definition edits. Timers survive restart and catch up lazily before
relevant presentation, interaction, Craft and travel/load checks. No global polling
or general scheduler is present. Morphs preserve the object ID and valid placement;
quantity must be one, and corpse/specialized-state morphs are excluded.

Thomas's explicit clarification applies: **anything embedded in a morphing object
disappears atomically when that object morphs or disappears**. An embedded object
may itself morph while retaining its own parent. Terminal disappearance removes
its ground attachment and frees its hand. Ordinary rabbit death, corpse, SKIN and
knife recovery are unchanged because corpse morphs remain excluded.

Migration `20260930_07` adds prototype revision/editor/time and per-instance pending
morph deadlines plus the deadline index. It does not start old timers, spawn objects,
change placement or reset existing game state. PostgreSQL authoring transaction
locking prevents concurrent edits from introducing a cycle; ordinary interactions
use scoped lifecycle row locks and current-stage checks. See the
[governing Object Builder/morph contract](objects.html#20-object-builder-and-timed-morph-v1-2026-09-30).

## Verification

Implementation revision: [5a4ed86](https://github.com/twpaige/Crown-Call/commit/5a4ed862a1bb212e5502df34ec388d406440ed84), committed and pushed to main.

- Focused Craft/Object/Morph/Builder/material run: **157 passed** in 109.04 seconds.
- Final expanded Object Builder/morph run: **37 passed** in 32.90 seconds, including
  API validation/authorization/revision protection, cycles/depth/timer validation,
  immutable schedules after edits, non-portable Craft output, linear/offline catch-up,
  terminal disappearance, identity/ground/ROOM/hand/embedded placement, restart,
  retry receipts, atomic embedded deletion/rollback, concurrent GET and graph edits,
  migration preservation, changed/expired APPROACH targets and position revalidation.
- Directly affected gameplay regressions: **245 passed** in 160.52 seconds: rabbit
  SKIN, wounds, THROW/REMOVE, Hunting Knife, player APPROACH, world, cabin, water
  movement, stamina recovery and odometer. Together these runs cover **404 distinct
  pytest cases**, not the full suite. SQLite tests use isolated databases and
  independent connections; the existing Starlette/httpx deprecation warning remains.
- A final API create/edit/catalog/authorization check passed after tightening
  revision-consistent catalog reads and save responses.
- Headless Edge/Playwright checks pass for both compact Builder tabs, form payloads,
  revisions/reload, server errors, desktop and mobile overflow; existing client
  login/arrival and navigation/request-ID/role visibility checks also pass.
- Changed-file Ruff, whitespace, single Alembic head, SQLite migration execution,
  and PostgreSQL offline migration SQL compilation pass. No PostgreSQL integration
  database or live DEV/PROD database was used.

## Deployment state and next action

No DEV/PROD deployment, migration or restart was run for this increment. Thomas's
last pasted deployment evidence was FAST DEV `575b87bb4d4ba3fce66e3ae41e49089078198a1c`;
no later live revision is claimed here. Documentation publication is separate.

After the final documentation pin is pushed, Thomas can deploy that committed
revision and use Builder/admin access to create ordinary object prototypes, connect
linear morph stages, and author a ground-output Craft to create a test instance.
Test LOOK after elapsed deadlines, restart catch-up, portability and recovery before
expiry. A save alone never creates a physical instance. The
[deployment guide](development.html#cc-update-authority-and-installation) owns
`sudo cc-update dev --fast [ref]` for iteration and `sudo cc-update dev [ref]` for
full verification. No fast PROD. Default development checks remain focused tests
and directly affected regressions; run the full suite only for sufficiently broad
changes or Thomas's explicit request.

## Deferred

Wearables, containers, armor, durability, MOB Builder/CCAI clients, repeating/circular
morphs, specialized-state morphs, general scheduling/background cleanup, ignition,
fuel consumption, roasting, EAT, tanning, butchering, cabin construction, broad
resources/depletion, quality, recipe discovery and skill progression remain deferred.
