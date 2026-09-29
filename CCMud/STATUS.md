---
title: Current status
description: Pioneer Cabin door and entry restored through a reviewed HOG attachment; deployment remains manual.
reviewed: 2026-09-28
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Pioneer Cabin corrective work starts from private `22a66c7` (Cormac v1.1).
The original cabin survives in persistent geometry, but the HOG adapter left its
door at absolute Z=0 while characters stood on generated ground near Z=17,156.
Outdoor perception/entry gates and later water-only ENTER dispatch compounded this.
The [cabin contract](hog.html#pioneer-cabin-attachment-and-regression-invariant-2026-09-28)
records the introducing changes and safety invariant.

The exact seeded cabin now has a reviewed, fail-closed runtime ground attachment.
Its stored coordinates, dimensions, legacy behavior and full collision footprint
are preserved. Nearby LOOK exposes the audited cabin and door state. OPEN requires
three-dimensional reach and a certified path to the entrance without an intervening
wall. ENTER CABIN resolves the place before water, requires an open door, and clears
outdoor travel state. EXIT and interior reconnect retain the correct attachment.
Other structures remain unaudited; continuous interior travel is still unavailable.

## Verification and next action

Implementation pushed: `5adebb8ae781af23a061127f352bb95eea17d682`.
Full suite on this implementation: **509 passed**, 13 dependency deprecation warnings,
**587.65s**. Final focused cabin/world/water/field commands and
HOG walking/runtime/real-field/Cormac: **210 passed**, 1 dependency deprecation warning,
**111.06s**. Fourteen new cases cover real-seed entry, exit, reconnect, stopped travel,
five wall/distance positions, and seven audit failures, including explicit authored
GroundPlacement overrides. The unchanged baseline reproduces all three reported
OPEN/ENTER/LOOK failures at the entrance; the same reproduction passes with this fix.
Changed-file Ruff and whitespace checks pass. Repository-wide Ruff reports **55 existing
findings**, also reproduced in an untouched `22a66c7` export.

No DEV or PROD deployment, cc-update invocation, or live database migration occurred.
The scenario uses seed `867359018957601` and original coordinates in isolated SQLite
tests; this is not live gameplay verification. After review, Thomas can manually update
DEV to the final pushed private commit. PROD remains unauthorized.

## Preserved baseline and limits

Cormac v1.1 retains stable factual wilderness prose, landmark identity deduplication,
BRIEF/NORMAL/MAXIMUM session verbosity and separate RAW/admin diagnostics. The nearby
audited cabin description is supplied by persistent structure facts; Cormac's natural
geography classification and cache are unchanged. No targeted LOOK was added.

Water Movement v1 remains LAND/WADING/SWIMMING/FLOATING with persisted AUTOWADE/AUTOSWIM,
0.1-second integration and configurable 15-second observations. Stationary FLOAT does
not emit movement observations. Disconnect acts like STOP, with normal offline rest
and no offline drift. Drowning and knockdowns remain deferred. Riffles remain descriptive;
underlying waterway geometry governs traversal. Other unsupported natural features stay
fail-closed, including ravine reservations and certification retries.

Live DEV/PROD revisions were not inspected. Prior deployment reports are historical
evidence, not verification of this correction. Public documentation publication and
private reference synchronization are separate from game deployment.
