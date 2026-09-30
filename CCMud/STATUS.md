---
title: Current status
description: Bounded rabbit corpse SKIN into persistent carcass, raw pelt and guts.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

`SKIN CORPSE` extends the existing THROW / wound / death / corpse / REMOVE loop.
An accessible rabbit corpse may be in the character's hand or on the ground within
normal interaction range. A suitable cutting tool must be held in either hand;
the Hunting Knife qualifies and stays in its original hand. Embedded items must
be removed first. Processing consumes the corpse and creates exactly three persistent
OBJECTS: rabbit carcass, raw rabbit pelt and rabbit guts, all at the character's feet.
No invisible inventory or automatic hand juggling. LOOK, GET and DROP use normal
physical-object rules, including inside the cabin. See
[object architecture section 17](objects.html#17-rabbit-corpse-skin-2026-09-30).

The corpse claim, deletion and output creation share one transaction. Conditional
location/tool checks and the single corpse claim prevent duplicate processing
through retries or concurrent players; failures restore the input and all outputs.
Each output receives its own persistent identity. The knife's identity is unchanged.
Existing visibility, HOG ground attachment, travel-stop and ROOM rules are reused.
Migration `20260930_04` adds three output prototypes/portability and the Hunting
Knife's cutting capability. It does not spawn or relocate world objects.

Prior rabbit combat remains unchanged: weapon severity modifiers, unarmored +1,
MOB severity +2, persisted/combined wounds and rabbit MORT immediate death.
Ordinary character death rules are unchanged. A dead rabbit is not respawned.

## Verification

Implementation: [0073ef0](https://github.com/twpaige/Crown-Call/commit/0073ef0d3838904b2b182865ec7ac24f3b5a7373), committed and pushed to main.

- Focused SKIN suite: **22 passed**. Covers held/ground input, occupied hands,
  held cutting-tool requirement, inaccessible targets, unsupported placement,
  all three outputs at the feet, consumed input, tool identity, restart/LOOK,
  cabin GET, travel stopping, rollback, concurrent processing and transfer races.
- Combined object/wound/approach/world/cabin/walking/water/travel/odometer regressions,
  including SKIN: **302 passed** in 182.25 seconds.
- Cormac, Eyes and knowledge-base synchronization regressions: **74 passed** in
  20.74 seconds. Total selected coverage: **376 passed**; each run reported one
  existing dependency deprecation warning. Tests use isolated SQLite, never live data.
- Changed-file Ruff and whitespace checks pass. Alembic has one head, `20260930_04`.
  Repository-wide Ruff reports 55 existing findings outside the changed files.
- The complete pytest suite was not rerun for this bounded increment. The prior
  wound/corpse milestone passed 768 full-suite cases plus its additional focused
  disabled-autoflush/foreign-key regression. That is historical evidence, not a
  claim of full-suite verification for the current SKIN revision.

## Deployment state and next action

Thomas's last pasted server evidence verified FAST DEV revision
`575b87bb4d4ba3fce66e3ae41e49089078198a1c`: release `575b87bb4d4b`, service active,
health ready, database up, HOG walking certified_origin. Later server revisions
have not been verified by this task. No server command, migration, restart or
DEV/PROD deployment was run for this increment.

After the final documentation pin is pushed, Thomas can deploy that committed
revision and playtest APPROACH CORPSE -> REMOVE KNIFE CORPSE -> SKIN CORPSE -> LOOK.
GET CORPSE before SKIN is optional; the knife must remain held. Outputs always
land at the player's feet. A cleaned carcass supports normal GET but no cooking yet.
The [deployment guide](development.html#cc-update-authority-and-installation) owns
`sudo cc-update dev --fast [ref]` for iteration and `sudo cc-update dev [ref]` for
full verification. Use full DEV periodically and at feature/milestone completion.
No fast PROD exists. Game deployment is separate from documentation publication.

## Deferred

Tanning, butchering, Craft, work timers, spit-making, fire, cooking, EAT, decay,
bleeding simulation, MOB AI/chasing, other species' profiles and broader combat
or resource systems remain outside this increment. The carcass is reserved for
later whole roasting. A new test rabbit/reset needs separate content authorization.
