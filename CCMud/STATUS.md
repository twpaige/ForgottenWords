---
title: Current status
description: Bounded THROW wounds, rabbit death and persistent corpse handling.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

THROW → wound → death → corpse extends the existing Rabbit MOB/THROW/REMOVE
interaction. Thomas explicitly adopted current weapon severity modifiers, then
replaced the proposed DEEP death threshold with a **+2 MOB severity modifier**.
The final rule applies weapon SM, armor, then MOB severity, capped at MORT.
For Hunting Knife +0 against an unarmored (+1) rabbit with MOB +2, MoV 1–4 gives
SEVR, 5–8 GRAV and 9+ MORT. Survivor wounds persist and combine under W8; C5 reduces
subsequent dodge. A rabbit at MORT dies immediately, including combined wounds.
Ordinary character MORT/bleed-out rules remain unchanged. The exact contract is
[object architecture section 16](objects.html#16-throw-wounds-death-and-corpse-2026-09-30).

Death atomically removes the active MOB, creates a generic CORPSE OBJECT at its
supported ground location, and transfers all embedded items to the corpse without
changing their IDs. Current corpse name, species and weight persist. Conditional
wound updates prevent duplicate deaths; failed transactions restore the prior MOB
and knife state. No HP, special kill command, replacement knife or rabbit respawn.

LOOK and inventory show the corpse and embedded knife. Ordinary APPROACH, GET,
DROP, free-hand/portability checks and cabin transitions apply. REMOVE works from
a visible ground corpse or one's own held corpse. Another player's pickup prevents
removal. Existing perceptible OBJECT/MOB APPROACH keeps normal arrival prose and
interaction range; HOG generation, travel, cabin and water authority are preserved.

Migration `20260930_03` retains existing knife/rabbit identity and hand/ground/embedded
placements, adds wounds and revision checks, gives existing rabbits severity +2 with
no retroactive wounds, and adds corpse details/embedded-object constraints. No live
migration or deployment has been performed for this increment.

## Verification

Focused wound/corpse suite: **27 passed**, one dependency deprecation warning,
30.88 seconds. Changed-file Ruff, whitespace and Node client login/arrival checks
pass. Full suite: **768 passed**, 13 dependency deprecation warnings, 655.84 seconds.
No application code changed after that run began. The 27-case focused run includes
one additional regression for the runtime's disabled-autoflush policy with foreign
keys enforced, covering all 769 current cases together with the full run.
Implementation: [0d4886f](https://github.com/twpaige/Crown-Call/commit/0d4886fc10cf3f0491747a4f45290138a2e8d3e6), committed and pushed to main.
The focused wound/corpse tests exercise survival, lethal and combined wounds,
restart, knife identity, corpse GET/REMOVE, cabin handling, rollback at transfer and
commit, duplicate death, competing fatal throws and removal/pickup races. Migration
checks cover existing ground, held and embedded knives. Tests use isolated SQLite
and the real HOG DEV seed, never a live database.

## Deployment state and next action

Thomas's pasted server output verified FAST DEV revision `575b87bb4d4ba3fce66e3ae41e49089078198a1c`
before this increment: current release `575b87bb4d4b`, service active, `/health`
ready, database up, HOG walking `certified_origin`. The installed updater's hash
matched the committed source. That fast run skipped pytest as designed; it is not
full milestone verification and contains no new wound/corpse implementation.

The [deployment guide](development.html#cc-update-authority-and-installation) owns
`sudo cc-update dev --fast [ref]` for iteration and `sudo cc-update dev [ref]` for
full verification. No fast PROD. No server command, migration, restart or deployment
was run by this task. After review, Thomas can deploy a pinned revision and playtest
LOOK → APPROACH KNIFE → GET KNIFE → THROW KNIFE RABBIT. On death, APPROACH CORPSE,
REMOVE KNIFE CORPSE and GET CORPSE; a held corpse also permits REMOVE with a free hand.
Use full DEV periodically and at feature/milestone completion.

## Deferred

SKIN, carcass/pelt/guts, butchering, decay, fire, cooking, food, bleeding simulation,
MOB AI/chasing, other species' wound/death profiles, healing and the full combat
encounter/turn system remain outside this increment. Death does not respawn the
rabbit; any new test animal/reset requires separate content authorization.
