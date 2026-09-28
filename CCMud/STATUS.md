---
title: Current status
description: Bounded water geometry integration; player-facing swimming remains deferred.
reviewed: 2026-09-28
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

The September 28 water geometry integration starts from private reviewed baseline
`b327df339ff7c6acffc73d6afce2d63e9d238c8f`, incorporating the existing Waterway
Geometry v1 implementation at `5272396`. It corrects maximum depth to **25 ft**,
adds ordered analytic bank/depth-contour events, and separates geometry certification
from ordinary standing permission. Read the [current contract](hog.html#water-geometry-integration-2026-09-28).

The parabolic depth/current profile and stable current hashing remain unchanged.
Cache identity uses waterway_geometry=2; previous occupants revalidate against current
geometry. No HOG generator output, movement controller, ground Z, integration timing,
observation timing, stamina, or player-facing water command changes are introduced.
Ordinary movement still permits <=3-ft water and blocks deeper water. Certification
of deeper water does not itself authorize movement. Unsupported lake/ravine geometry
and independent terrain/structure hazards retain their safety checks.

## Verification

Implementation committed/pushed at `cf6450e90a3534e33782663f99cc71e891c999f4`.
[Integration report](https://github.com/twpaige/Crown-Call/blob/c07999c5d5302d1c62007868aa836c40db64c96e/docs/water-geometry-integration.md)
and [benchmark samples](https://github.com/twpaige/Crown-Call/blob/cf6450e90a3534e33782663f99cc71e891c999f4/docs/water-geometry-integration-benchmark.json).

Focused water suites: **49 passed**, 1 warning, 45.76s. Expanded query suite:
**18 passed**, 0.17s. Full suite on final implementation: **387 passed**, 13 upstream
deprecation warnings, 404.27s. Changed source/tests/benchmark pass Ruff.

Sequential warm benchmark after pytest: first entry **17.946 microseconds** versus
**21.109** for the previous implementation; point sample **9.649**; all bank/1-ft/4-ft
events **65.364**. Seven batches of 2000 calls replay the preserved real major river.
These exclude terrain generation, database/network costs and live load testing.
The real river has corrected center depth **25 ft**, unchanged center current
**4.927746122474707 mph**, and ordered bank/1-ft/4-ft entry then exit events.
Its current three-foot stop contour is **868.2179149109494 ft** east of
[70282734,-31352345]. Historical measurements remain labeled separately.

## Environment and next phase

No DEV/PROD deployment, live database access or live gameplay verification is part
of this pass. Active runtime revision is not checked here. Publication and automated
tests are separate from game deployment; PROD has no release authorization.

Return this geometry integration for review before implementing WADE/SWIM/FLOAT,
AUTOWADE/AUTOSWIM, SET/STATUS, water prose, stamina changes or current-driven movement.
The next movement layer must apply its own mode policy to certified positions,
handle enter/exit/touch events and strict depth comparisons, and compose chunk-local
queries without duplicate seam events. Existing independent hazards remain mandatory.
Lakes/coasts/oceans and absolute water-surface/riverbed Z remain outside this pass.

Earlier investigation and release evidence remain accessible through [Evidence](evidence.html).
