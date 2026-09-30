---
title: Current status
description: MAKE SPIT and GATHER FIREWOOD with physical outputs and minimal Work Timer accounting.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

`MAKE SPIT` and `GATHER FIREWOOD` add the next two basic survival actions.
Existing HOG light/heavy woodland classification supplies suitable fallen wood
without individual tree/branch/resource nodes. The current position must also
pass the existing dry-ground placement checks. Interiors, open grassland,
water and unavailable/unsupported ground fail plainly. No special CAP, recipe,
cutting tool or empty hand is required.

GATHER creates one 5 lb firewood bundle; MAKE creates one 8 oz wooden spit. Both
are persistent portable OBJECTS on the ground at the character's feet. Existing
LOOK, APPROACH, GET and DROP apply; held possessions are unchanged. The intended
next use is campfire fuel and whole-rabbit roasting, neither implemented yet.
See [object architecture section 18](objects.html#18-make-spit-and-gather-firewood-2026-09-30).

Minimal Craft integration now persists one per-character real-time `work_until`.
Each output appears immediately and adds five minutes to max(now, work_until), in
one transaction. Entry requires remaining debt strictly below 48 hours; an accepted
action may cross that threshold. Offline time reduces debt; travel/world acceleration
does not. Success text reports remaining minutes. A conditional labor/position
update rejects stale concurrent attempts with no output or debt. Repeated deliberate
commands remain new charged work under C02, not a per-recipe cooldown. The current
protocol has no command retry token or automatic replay.

Migration `20260930_05` adds the nullable timestamp and two portable definitions,
without spawning outputs or changing existing objects. The prior rabbit THROW,
wound, corpse, REMOVE and SKIN interactions remain unchanged. SKIN continues to
use its explicitly bounded immediate processing rule; no retroactive debt is added.

## Verification

Implementation: [733e847](https://github.com/twpaige/Crown-Call/commit/733e84710f3a314a06bc324244f988f4dddc401c), committed and pushed to main.

- Focused survival-material suite: **31 passed** in 23.27 seconds, exercising the
  actual DEV-origin woodland, object identity/location/weight, full hands,
  LOOK/GET and restart, unsuitable/unavailable terrain, additive migration,
  Work Timer entry threshold, offline elapsed time, repeated charged work,
  rollback, concurrent stale attempts, position revalidation and travel stopping.
- Combined appropriate regressions: **415 passed** in 230.28 seconds. Includes
  the new suite, rabbit SKIN/wounds/THROW, Hunting Knife, player APPROACH, world,
  cabin, HOG walking, water movement, travel, odometers, stamina recovery,
  authentication, Cormac and Eyes. One existing dependency deprecation warning.
  Tests use isolated SQLite, never live data.
- Changed-file Ruff and whitespace checks pass. Alembic has one head,
  `20260930_05`. The complete pytest suite was not rerun for this bounded increment.
- Prior SKIN milestone: 376 selected tests passed at implementation `0073ef0`;
  final documentation pin `6ff847e`. This is historical evidence, not a claim of
  current full-suite or live-server verification.

## Deployment state and next action

Thomas's last pasted server evidence verified FAST DEV revision
`575b87bb4d4ba3fce66e3ae41e49089078198a1c`: release `575b87bb4d4b`, service active,
health ready, database up, HOG walking certified_origin. Later server revisions
have not been verified by this task. No server command, migration, restart or
DEV/PROD deployment was run for this increment.

After the final documentation pin is pushed, Thomas can deploy that committed
revision and playtest MAKE SPIT -> GATHER FIREWOOD -> LOOK, then ordinary GET/DROP.
Both products appear immediately at the character's feet. No fire can be lit yet.
The [deployment guide](development.html#cc-update-authority-and-installation) owns
`sudo cc-update dev --fast [ref]` for iteration and `sudo cc-update dev [ref]` for
full verification. Use full DEV periodically and at feature/milestone completion.
No fast PROD exists. Game deployment is separate from documentation publication.

## Deferred

Ignition, burning/fuel consumption, roasting, EAT, tanning, butchering, decay,
broad gathering, a crafting catalog, learning, bleeding simulation, MOB AI/chasing
and broader combat/resource systems remain outside this increment. The rabbit
carcass is reserved for later whole roasting. A new test rabbit/reset still needs
separate content authorization; dead rabbits do not respawn.
