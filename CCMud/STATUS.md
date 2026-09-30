---
title: Current status
description: Bounded Rabbit MOB, THROW and REMOVE hit test; no game deployment.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Thomas authorized **Rabbit MOB + THROW**, explicitly reduced the outcome to hit/miss
without wounds or killing, and then added **REMOVE**. CCMUD calls NPCs MOBs (mobiles).
The exact contract is [object architecture section 15](objects.html#15-rabbit-mob-throw-and-remove-hit-test).

The migration adds one persistent rabbit 70 feet east of Origin (X=840, Y=0),
RANGE default 0, the Hunting Knife's throwable capability, and an embedded-MOB
object location. The existing knife retains its ID. LOOK never spawns either.
A physical hand is sufficient; WIELD is not required and no WIELD system is added.

THROW uses adopted opposed d20 + RANGE + knife modifier (+2), minus one per full
ten feet, against d20 + ATHLX. Minimum distance is ten feet; ties miss. A miss leaves
the knife on supported ground at the target's feet. A hit embeds it in the rabbit.
REMOVE requires current visibility, the existing interaction range and a free hand,
then returns that same knife to the right hand first, otherwise the left.
Full hands and invalid targets leave possessions unchanged. Both commands use
existing STOP semantics and never close distance automatically.

Object identity and exclusive hand/ground/MOB placement persist across restart.
Conditional transfers and unique hand occupancy protect competing actions. MOB sight
uses the existing bounded ready-ground geometry checks; Nearby presentation stays
outside cached geographic prose. HOG generation, cabin/water barriers, travel and
odometer authority remain unchanged.

## Verification

Runtime implementation: [8010b6c](https://github.com/twpaige/Crown-Call/commit/8010b6cfbc38898df5e699d4ac1a9ef1ecc4d9c2), based on `3af197f22cb639c2853a27abd5100bc3058e84c7`.
Focused rabbit tests: **32 passed**, one dependency warning, 24.74 seconds.
Earlier rabbit/knife combined run: **48 passed**, one dependency warning, 45.81 seconds.
The later rabbit run adds the authored Origin loop and REMOVE rollback coverage.
Full suite: **695 passed**, 13 dependency deprecation warnings, **588.30 seconds**.
The subsequent 32-case rabbit run covers the newly added Origin loop and REMOVE
rollback case; the final migration run verifies both ground and held starting states
(**2 passed**, 1.35 seconds). Together these runs cover all 698 current cases.
Changed-file Ruff, whitespace and the existing Node client-login timing checks pass.
No application code changed after the full suite started; the additional cases extend tests.
Tests use isolated SQLite databases and the real HOG DEV seed, never a live database.

## Limits and next action

This is the bounded hit test, not a completed hunt or combat encounter engine.
Wounds, bleeding, death, rabbit AI, turn scheduling, corpse processing, SKIN, fire,
cooking, food, containers and other object slices remain deferred. Tabletop severity
modifier changes are not adopted implicitly; no wound rule is applied by this increment.

After review, Thomas can deploy DEV manually and exercise LOOK → APPROACH KNIFE →
GET KNIFE → THROW KNIFE RABBIT. On a hit, move within 15 feet and REMOVE KNIFE RABBIT;
on a miss use ordinary APPROACH/GET. Check inventory and restart persistence.
The earlier Slice 1 deployment prerequisites remain in README-APP.md; no live cleanup,
`cc-update`, migration, DEV deployment or PROD deployment has been performed here.

The prior bug fix `3af197f` repairs object APPROACH controller wiring, with an actual
startup/travel-tick regression, and adds immediate character-entry connection feedback.
Live environment revisions are not inferred from source pushes. Update reports include
an exact `sudo cc-update dev <pushed-commit>` command for Thomas to run.
