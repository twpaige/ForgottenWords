---
title: Current status
description: Cormac v1 provides stable, factual wilderness LOOK prose; game deployment remains separate.
reviewed: 2026-09-28
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Cormac v1.1 is a narrow correction from private baseline `b5f282f`.
Local lake shorelines and regional discovery previously supplied the same HOG identity
twice because only waterway IDs entered the adapter's exclusion set. Local restrictions
now use their authoritative feature ID and exclude matching regional entries; Cormac
also deduplicates by ID before landmark selection. Distinct IDs remain distinct.
Light-woodland titles now say **Among Light Woodland**. Dynamic distance bands, session
verbosity, RAW/admin diagnostics, movement and water geometry are unchanged.
Read the [Cormac contract](hog.html#cormac-v1-2026-09-28) and [command review](commands.html).

LOOK supplies a playerless descriptive title/body. `LOOK BRIEF|NORMAL|MAXIMUM` selects
a session preference, initially NORMAL. Stable descriptive scenes reuse cached prose;
small numeric changes and threshold jitter do not continually rewrite the description.
Local water geometry decides inside-versus-near-water. Nearby HOG candidates use the
explicit temporary ordinary-perception assumption; diagnostic unavailable LOS is not
interpreted as invisibility. No distant certified sight or invented atmosphere is claimed.
Admin RAW remains separate and available. Periodic travel narration is unchanged.

Implementation pushed: `7ea60629ef00ace45b4d228f1ac0b12847f3298d`.
Full suite: **495 passed**, 13 dependency deprecation warnings, **442.71s**.
Focused Cormac/field/water suites: **137 passed**, 1 warning, **13.10s**.
Seven new regression cases cover duplicate input IDs, local/regional lake identity and
aliases, preservation of distinct lakes, stream/lake combinations, changing distances
and dominant titles across movement. Existing verbosity, water, RAW/admin and stable
scene tests remain passing. Changed-file Ruff, whitespace and browser presentation
checks pass.

## Known limits and next action

No DEV or PROD deployment, cc-update invocation or live database migration occurred.
Thomas reports Cormac v1 deployed and working in DEV. The active live revision was
not independently checked in this corrective task. Local tests and documentation
publication do not establish live gameplay verification. Review the implementation
before a separately authorized/manual DEV release.

Cold source catalogs may initially omit a nearby landmark, then enrich the description
when existing background queries finish. Uncertain bank geometry is omitted. Verbosity
and recent scene/language memory are session/process-local. Sparse scenes stay short.
CORMAC uses fixed templates; vocabulary and classification live in cormac.py. There is
no LLM, grammar engine, weather invention, full travel narrator or new LOS implementation.
Cormac v2 should refine allowed-feature/perception inputs and useful geographic detail,
then introduce controlled wording variation only for genuinely changed scenes.

## Preserved gameplay baseline

Water Movement v1 remains LAND/WADING/SWIMMING/FLOATING with persisted AUTOWADE/AUTOSWIM,
0.1-second integration and configurable 15-second observations. Stationary FLOAT does
not emit movement observations. Disconnect acts like STOP, with normal offline rest
and no offline drift. Drowning and knockdowns remain deferred.

Riffles remain descriptive/nonblocking; actual waterway geometry governs traversal.
The prior riffle implementation `f4892b3` and pinned release `ea9e4d4` had 465 passing
tests. Other unsupported natural features remain fail-closed, with existing ravine
reservations and five-second retry behavior. Earlier release evidence is preserved in
Git and [Evidence](evidence.html); Thomas reported successful manual DEV water testing
on the earlier `3e8f5c7` baseline. This task does not independently reverify that deployment.
