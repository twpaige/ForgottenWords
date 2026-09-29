---
title: Current status
description: Reviewed regional biome transitions remain certified and traversable; deployment remains manual.
reviewed: 2026-09-29
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Reviewed regional biome transitions are implemented on private baseline `e121d7e`.
The newer cabin, navigation, shortcuts and upstream ruleset changes are preserved.
Walking policy version 5 validates every intersecting regional cell, including narrow
strips between ground samples. Reviewed surface changes become safe movement boundaries;
missing/unsupported geography and independent hazards still fail closed.

The seed `867359018957601` woodland/mixed-forest transition in chunks `[60-63,11]`
remains traversable. Local 42-degree and reverse 222-degree travel tests continue
without confirmation or stopping, preserve WALK/stamina, and refresh factors from
85% light woodland to 65% heavy woodland and back at integer-inch crossing precision.
See the [transition contract](hog.html#reviewed-regional-biome-transitions-2026-09-29).

The old generation `1f14772c2e663ce762201381df0fba4f46ff47a8a0363929447afe72c3f33285`
remains accepted for character revalidation, while walking certificates regenerate
under version 5. Biome/elevation grids match pre-fix fingerprints; no saved coordinates,
source geography or database schema changes.

## Verification and next action

Implementation pushed: `f6daa784aea30fca17bb1e7dacd7e590ddc64476`.
Full suite on the combined baseline: **571 passed**, 13 dependency deprecation
warnings, **490.12s**. Expanded focused checks before upstream integration: **128 passed**,
1 warning, 101.68s. Final-baseline boundary/terrain/Cormac checks: **76 passed**,
1 warning, 2.79s. The 25 new regression cases include the exact seed, forward/reverse
travel, factor refresh, narrow strips, corner crossings, missing classes, old certificate
rejection, saved-position validation and unchanged source-grid fingerprints.
Changed-file Ruff and whitespace checks pass. The existing CLEAR browser check passes.
The inherited travel-presentation extraction script fails because its isolated context
omits the newer positionText helper; script and client are unchanged from e121d7e.
This is recorded separately from the passing Python suite; no live browser claim is made.

No DEV or PROD deployment, cc-update invocation, or live database migration occurred.
Thomas will manually deploy the reviewed final private revision. Local automated tests
are not live gameplay verification. Documentation publication is separate from game deployment.

## Preserved baseline and limits

The cabin's reviewed ground attachment, coordinates, collision footprint, door reach,
OPEN/ENTER/EXIT and interior reconnect behavior remain intact. Other structures are
unaudited; this does not add continuous indoor movement or route pathfinding.

Cormac v1.1 prose, verbosity, stable feature identity and RAW diagnostics are unchanged.
Water Movement v1 retains LAND/WADING/SWIMMING/FLOATING, AUTOWADE/AUTOSWIM, ordinary
currents, certification and hazard restrictions. Completing a swimming distance uses
normal floating behavior. Disconnect still stops travel with no offline drift.
Drowning, knockdowns, unsupported natural features and future nine-zone expansion
remain deferred. Only the walking-certificate version changes; world generators, units and schema remain unchanged.
