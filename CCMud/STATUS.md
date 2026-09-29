---
title: Current status
description: Cormac clockwise LOOK and independently refreshed Nearby; deployment remains manual.
reviewed: 2026-09-29
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Cormac's wilderness LOOK now establishes HERE, then composes selected geographic
landmarks clockwise from north using the absolute eight-point compass. Selection still
uses the complete supplied candidate set and nearest/dominant priority; verbosity
controls density, not a northern directional bias. Missing directions create no filler.
Existing stable feature identity collapses duplicate representations; distinct features
remain distinct. Immediate water, title relationships, approximate distance updates,
playerless prose and descriptive hysteresis remain intact.

Nearby is a separate current-observation display, ordered clockwise and then nearest
within each sector. All verbosity modes retain supported entities. The existing audited
Pioneer Cabin, within its original observation range and with live door state, is the
current supported subset. Generic wilderness characters/NPCs/items await a trustworthy
perception feed. Nearby never enters the geographic cache. RAW/Position stay separate.

HOG supplies local terrain/vegetation/slope, represented water and nearby geographic
bearings. It does not supply a certified eight-sector vegetation/elevation survey.
The implementation describes available facts, with no new generation or invented
spatial variation. See the [Cormac contract](hog.html#clockwise-look-and-nearby-2026-09-29).
Travel observations retain their existing behavior; future travel prose should describe
meaningful changes rather than repeat LOOK surveys.

## Verification and next action

Implementation pushed: `655c621e204611e2d561bbd48174750e13a0a982`, from private baseline
`15a294ca39371a77160cb8a79d29eb59606669fb`.

Focused Cormac/cabin tests: **58 passed**, one dependency warning, **31.03s**.
Complete suite: **607 passed**, 13 dependency deprecation warnings, **541.64s**.
Changed-file Ruff and whitespace checks pass. Fourteen new regression cases cover
clockwise composition without north-biased selection, adjacent duplicate features,
water-first/deeper-water ordering, shared compass edges, live independently formatted
Nearby, cabin door state, verbosity, heading independence and empty-section omission.
Existing movement, water, admin/RAW and persistence tests remain green.

Twelve actual-seed LOOK examples were captured through HogGame/WorldService with isolated
SQLite state: all verbosity levels at the cabin, one stream, two distinct streams, and
riffles alongside a river. No synthetic geography or live server was used.

No DEV or PROD deployment, cc-update invocation, or live database migration occurs in
this phase. Thomas will manually deploy the reviewed revision.

## Preserved baseline and limits

Persistent ODOMETER/TRIP and the browser COPY/CLEAR/RESET TRIP controls remain in place.
Migration `20260929_01` from that earlier phase still initializes existing characters'
counters at zero; this LOOK pass introduces no additional migration.

The cabin's ground attachment, footprint/collision, door reach, OPEN/ENTER/EXIT and
interior reconnect behavior are unchanged. Water Movement v1, certification, movement,
stamina, terrain factors and offline recovery remain separate from presentation.
Version-5 walking certificates, world-generation identity, saved coordinates and HOG
geography remain unchanged. Advanced LOS, directional vegetation surveys, generic entity
perception, language variation and full travel narration remain future work.
