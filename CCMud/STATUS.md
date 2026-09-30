---
title: Current status
description: Object architecture Draft 5 awaits final review; regional lake implementation remains the latest verified game baseline.
reviewed: 2026-09-29
nav: status
permalink: /CCMud/status.html
---

## Current object architecture review

[CC_Objects.md — Working Draft 5](objects.html) records the approved terminology,
one current location path, ROOM-based DWELLING interactions, separate GET/APPROACH,
physical hands/containers, corpse processing, and six-slice stop line. It supersedes
Draft 4's fixed visibility limit and legacy DEV-possession preservation requirement.
The original private `CC_Objects.txt` is preserved as historical exploration.

**Next action:** review Draft 5 for final architecture approval, then prepare the
Hunting Knife implementation handoff. No additional foundational question has been
identified. Do not implement until Thomas separately authorizes it. No game code,
schema, live DEV cleanup, or deployment changed during this documentation task.

Review used private `e3491580ab952874f46cf9716a33f1bd0934e6f3` and public
`ea2c03d7a93f0458a9c8e4a28bb35fc135e742af`. Existing world/cabin/Cormac tests passed
78 cases with one dependency warning. Those tests verify the baseline, not the new
architecture. Draft Markdown, source links, and review-copy consistency were checked.
Future chats should read Crown-Call's WORK_START_HERE, this status, and the current
public draft; no Project chat context is required.

## Latest verified game implementation

Regional Lake Resolution & Major Water Scale starts from private `99fa30a`.
Tested implementation pushed: `210163fe725861f8e230fcdcd223e07647bc886f`.
The reported generation hash and failure were reproduced at both supplied positions.
The discovered local ID `867359018957601:lake:8226:0` is about 150 acres, not the huge
continental lake. The latter is `867359018957601:regional-lake:4`: 105 existing source
cells, 115,928.4 square miles, about 370 by 465 miles in represented extent.

Walking now resolves those existing cell footprints instead of refusing entire
shoreline chunks. Dry portions certify; lake water with unknown depth stays blocked.
Movement checks the safe side of an intercepted hazard before stopping, and confirmed
crossings still validate their destination. Discovery and Cormac share lake identity,
area, extent and exposed shoreline segments. Known lake scale replaces the ambiguous
combined biome label. No underlying lake placement or seed changes.

The [regional lake contract](hog.html#regional-lake-resolution-2026-09-29) owns the root
cause, diagnostic class semantics, physical area bands, limits and compatibility.
Walking adapter version 6 revalidates prior saved identities without relocating valid
dry positions. Lake interiors remain unsupported; no shallow profile is invented.

## Verification and next action

Final focused lake/travel/water tests: **115 passed**, one dependency warning,
**38.52s**. The final nearest-piece/wording checks add **42 Cormac passes** in
**2.43s**. Complete suite on the committed implementation: **646 passed**, 13 dependency
deprecation warnings, **700.48s**. Eleven new lake cases plus strengthened Cormac
deduplication tests cover source identity/extent, cell unions/islands, shoreline
certification, movement/JUMP denial, scale, all verbosity modes and zero surrounding
walking chunks from LOOK. Existing water, Eyes, river, Nearby, RAW/PROSE, Position,
odometer and cabin regressions pass. Changed-file Ruff and whitespace checks pass;
repository-wide Ruff exactly matches the earlier **55-finding** untouched baseline.

Isolated Windows/SQLite measurements from `scripts/benchmark_regional_lakes.py`: regional lake index about 158 KiB (110 cell
pieces and 72 exposed shoreline segments for five lakes); nearby chunks about 0.44s
each with unchanged 441 ground samples and one lake restriction per wet chunk.
Local footprint lookup median **0.0098ms**; cold LOOK **18.16ms**; warm LOOK median
**22.65ms**, p95 **23.68ms**. LOOK generates **zero** surrounding walking chunks and
the cache retains its 64-chunk bound. The index adds about 158 KiB of traced Python
allocations (not process RSS), peak about 176 KiB. Measurements are component/local
command timings, not live DEV latency guarantees. No full-lake walking raster exists.

## Limits and preserved behavior

The shoreline is the existing coarse cell-union boundary, not a surveyed bank. Unknown
lake depth/current means entry is still prohibited. Rivers retain their certified
water profiles and AUTOWADE/AUTOSWIM/WADE/SWIM/FLOAT/STAND mechanics. JUMP now accepts
valid dry ground in mixed shore chunks but still denies water and unresolved physical
reservations. A warning-only spring/natural-feature exception needs separate analysis.

Cormac Eyes, bounded observation, river identity deduplication, Nearby, RAW/PROSE,
BRIEF/NORMAL/MAXIMUM, admin Position, odometers and cabin interactions remain in scope
and passed regression verification. Advanced LOS, general prose polish, travel narration,
semicolon commands, finer lake bathymetry and future nine-zone work remain deferred.

No DEV or PROD deployment, cc-update invocation or live database migration occurred.
Thomas will manually deploy the final pushed revision. No live gameplay verification
is claimed; all evidence uses isolated fixtures and the deterministic source seed.

At `(78947938, 14572800)` inches, normal LOOK includes: “An inland-sea-scale lake lies
roughly two hundred feet to the east.” A manual DEV check after deployment can JUMP
to that dry point, LOOK, then move EAST 1000 feet: travel stops before the represented
lake boundary near X=78,951,442 inches, with unknown lake depth still blocking entry.
These coordinates are evidence/fixtures, never production special cases.
