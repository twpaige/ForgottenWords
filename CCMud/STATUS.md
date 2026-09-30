---
title: Current status
description: Fast DEV iteration mode and player APPROACH corrections; no agent deployment.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Deployment iteration tool

`sudo cc-update dev --fast [git-ref]` uses the existing immutable committed-release
machinery, dependency installation, forward migrations, activation, restart, health
check and code rollback. It skips pytest, runs inexpensive dependency consistency
checking, and prints **FAST / NOT FULLY VERIFIED**. Normal `sudo cc-update dev [git-ref]`
still runs the complete suite, including reused releases. No fast PROD is allowed.
See the [authoritative deployment guide](development.html#cc-update-authority-and-installation).

The installed updater must be refreshed from the tracked source only after any
currently running update completes. Thomas reported a DEV update in progress during
this work; its outcome/revision have not been inspected. No server file, process,
migration or deployment was touched by this task.

Updater verification: 37 isolated Bash control-flow tests plus 5 reference-sync tests
passed (42 total, 6.07 seconds),
covering parsing, root/lock safety, full/fast selection, dependency and migration
failures, release reuse and startup/readiness rollback. Bash syntax, changed-file
Ruff and whitespace checks pass. Server commands are stubbed; these tests do not
claim live Ubuntu deployment verification. Implementation: [96efe55](https://github.com/twpaige/Crown-Call/commit/96efe55b69986e2890449c0222bf4d9f6c9323ac), committed and pushed to main.

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

## Player APPROACH correction

APPROACH resolves perceptible OBJECTs and MOBs before the restricted admin geographic
catalog. Both use the existing continuous-travel controller and the same interaction-range
steering. Current object/MOB interaction range is 180 inches. Every tick revalidates
identity, fixed location and perception; movement or loss of the target stops travel.
There is no chasing or pathfinding. HOG feature targets retain their generation checks.

Ordinary arrival now says "You approach the Hunting Knife and stop." or
"You approach the rabbit and stop." The browser honors this server arrival text;
admin geographic diagnostics keep their existing separate wording.

## Verification

APPROACH runtime: [8895854](https://github.com/twpaige/Crown-Call/commit/88958549c53f368b4a768a33ef6d2b0becdcf9db), committed and pushed to main.
Focused and regression run: **328 passed**, one dependency deprecation warning,
182.28 seconds. Suites cover player approach, Hunting Knife, rabbit THROW/REMOVE,
field commands, admin/field navigation, travel, HOG walking, cabin, water movement
and odometer. The seven new cases cover object/MOB arrival and final interaction
range for players/admins, already-in-range arrival, moved targets, wall visibility
and cross-kind ambiguity. Five initial cases failed against the previous code.
Node client login/arrival rendering checks, changed-file Ruff and whitespace checks pass.
Earlier rabbit implementation: [8010b6c](https://github.com/twpaige/Crown-Call/commit/8010b6cfbc38898df5e699d4ac1a9ef1ecc4d9c2).
Its full-suite baseline passed 695 tests; subsequent focused cases brought coverage
to 698 cases before this APPROACH correction. Tests use isolated SQLite databases
and the real HOG DEV seed, never a live database.

## Limits and next action

This is the bounded hit test, not a completed hunt or combat encounter engine.
Wounds, bleeding, death, rabbit AI, turn scheduling, corpse processing, SKIN, fire,
cooking, food, containers and other object slices remain deferred. Tabletop severity
modifier changes are not adopted implicitly; no wound rule is applied by this increment.

After review, Thomas can deploy DEV manually and exercise LOOK → APPROACH KNIFE →
GET KNIFE → THROW KNIFE RABBIT. On a hit, APPROACH RABBIT and REMOVE KNIFE RABBIT;
on a miss use ordinary APPROACH/GET. Check inventory and restart persistence.
The earlier Slice 1 deployment prerequisites remain in README-APP.md; no live cleanup,
`cc-update`, migration, DEV deployment or PROD deployment has been performed here.

The prior bug fix `3af197f` repairs object APPROACH controller wiring, with an actual
startup/travel-tick regression, and adds immediate character-entry connection feedback.
Live environment revisions are not inferred from source pushes. Update reports include
an exact `sudo cc-update dev <pushed-commit>` command for Thomas to run.
