---
title: Current status
description: Persistent travel odometers and responsive transcript controls; deployment remains manual.
reviewed: 2026-09-29
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Persistent ODOMETER/TRIP and small web-client display controls are implemented on
private baseline `c0c260750d1ce69a822e80250a8115d852d35138`.
Counters record accepted travel through the position checkpoint; admin placement carries
no travel distance. Integer inches plus integer sub-inch carry survive reconnect/restart.
`ODOMETER` serves all clients; `TRIP RESET` resets only TRIP without stopping travel.
Migration `20260929_01` starts existing characters at zero; no historical travel is inferred.

The web HUD displays ODO/TRIP. RESET TRIP uses the server command, CLEAR reuses local
transcript clearing, and COPY captures all transcript entries with a separate notice.
Viewport sizing reserves space for input/controls. Saved shortcuts remain intact;
there was no existing Up/Down command-history subsystem and none is introduced.
See the [odometer contract](design.html#character-odometers-and-transcript-controls-2026-09-29)
and [commands](commands.html).

## Verification and next action

Implementation pushed: `a5e10ad50d3b00789fcea3c96620135262a6ad5e`.
Final full suite: **593 passed**, 13 dependency deprecation warnings, **513.41s**.
Expanded focused suite: **164 passed**, 1 warning, 77.75s. After retry-accounting
hardening, focused movement/water/lifecycle tests: **133 passed**, 1 warning, 11.50s;
final odometer suite: **22 passed**, 1 warning, 3.78s. Changed-file Ruff and whitespace
checks pass. The existing real-seed biome-crossing tests also verify distance accounting.

Headless Edge tests pass against actual client HTML with mocked account/WebSocket
responses: admin/player controls, complete transcript copy, unobtrusive success/failure,
local CLEAR, shortcut retention, HUD, and visible controls at 1366x768, 1280x720,
1024x600, 1920x1080 and 375x812. Existing CLEAR and travel-presentation scripts pass;
the inherited missing positionText helper in the latter's test context was corrected.
No unrelated runtime failure was found.

No DEV or PROD deployment, cc-update invocation, or live database migration occurred.
Thomas will manually deploy the reviewed revision; the normal updater applies the new
counter migration. Local browser tests use mocked account/WebSocket responses, not DEV.

## Preserved baseline and limits

The cabin's reviewed ground attachment, coordinates, collision footprint, door reach,
OPEN/ENTER/EXIT and interior reconnect behavior remain intact. Other structures are
unaudited; this does not add continuous indoor movement or route pathfinding.

Cormac v1.1 prose, verbosity, stable feature identity and RAW diagnostics are unchanged.
Water Movement v1 retains LAND/WADING/SWIMMING/FLOATING, AUTOWADE/AUTOSWIM, ordinary
currents, certification and hazard restrictions. Completing a swimming distance uses
normal floating behavior. Disconnect still stops travel with no offline drift.
Drowning, knockdowns, unsupported natural features and future nine-zone expansion
remain deferred. The existing version-5 walking certificate and world generators are unchanged. The
only schema change is the new character distance counters and fractional carry.
