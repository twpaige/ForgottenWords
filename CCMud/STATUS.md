---
title: Current status
description: Water Movement v1 DEV live-test cleanup; stationary observations and completion prose corrected.
reviewed: 2026-09-28
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Thomas reports Water Movement v1 baseline `3e8f5c74fe3def301dbbac87e3c06578e161465a`
deployed and manually verified on DEV against a real major river. Core entry, modes,
current displacement, settings and automatic bank-to-bank transitions worked.
This follow-up preserves those mechanics and fixes presentation only:

- Automatic movement observations require changed accepted coordinates; stationary
  FLOATING produces no periodic travel/RAW output. LOOK/STATUS and safety messages remain.
- STOP swimming emits one clear transition to floating, without a generic stop message.
- ENTER-water completes with ordinary prose using the resolved water feature kind.

Cleanup implementation pushed: `02140228843edde84a88a71582addbd5c5efd43f`.
Focused water/travel/world tests: **119 passed**, 1 upstream warning, 5.06s.
Full suite: **458 passed**, 13 upstream deprecation warnings, **399.74s**; no skips
or expected failures reported. Changed-file Ruff, whitespace and browser checks pass.

No certification, geometry, current physics, stamina, depth or auto-transition rules
changed. No migration added. This cleanup performs **no DEV/PROD deployment** and
runs no cc-update. Thomas manually deploys the reviewed fix.

## Water Movement v1 implementation baseline

Water Movement v1 is implemented and pushed at private commit
`bb6c15eb0280611f676b9d7eef3d1ca5e9830791`, from baseline
`31f44e2bb6531a4b7aef49aebd147ccf47f832d3`.
[Implementation report](https://github.com/twpaige/Crown-Call/blob/bb6c15eb0280611f676b9d7eef3d1ca5e9830791/docs/water-movement-v1.md)
and [benchmark samples](https://github.com/twpaige/Crown-Call/blob/bb6c15eb0280611f676b9d7eef3d1ca5e9830791/docs/water-movement-v1-benchmark.json)
require private repository access. Read the [current rules](hog.html#water-movement-v1-2026-09-28).

The existing server controller owns LAND/WADING/SWIMMING/FLOATING, precise ordered
water boundaries, current-vector displacement, stamina, WADE/SWIM/FLOAT/STAND/STOP,
SET toggles, STATUS and deliberate ENTER-water. Geometry, generator output, ground Z,
0.1-second integration and configurable 15-second observations remain unchanged.
Ordinary <=3-foot water walking is superseded: WADING <4 feet, SWIMMING >1 foot,
FLOATING any represented depth with current displacement only above 1 foot.
Independent terrain/structure restrictions remain active; certification is not permission.

Character mode and default-OFF AUTOWADE/AUTOSWIM are persisted by additive migration
`20260928_01`. Disconnect explicitly acts like STOP: swimming becomes FLOATING,
normal elapsed-time offline rest applies with no offline drift, and floating resumes
on reconnect. No offline tick loop, drowning or knockdown mechanics are added.

## Baseline verification

Final full suite: **449 passed**, **13 upstream deprecation warnings**, **431.68s**;
no skipped/expected failures reported. Focused water/travel: **91 passed**, 1 warning,
4.53s, including 61 new parametrized water cases. Real seeded river/creek, dry movement,
terrain/hazard, chunk and offline-recovery regressions pass. The earlier full run's
single ravine-message regression was corrected and verified in the final full rerun.

Changed-file Ruff and diff checks pass. Repository-wide Ruff has **55 pre-existing
findings in 12 untouched baseline files**. Node browser syntax, automatic-stop prose,
CLEAR isolation and server-authored water text rendering checks pass. Migration tests
use isolated SQLite; PostgreSQL and browser gameplay require the later live release.

Warm synthetic controller medians (microseconds/step): legacy optional-water-disabled
dry **47.498**, water-aware dry **122.800**, WADING **143.871**, SWIMMING **158.393**,
FLOATING **162.213**. Seven batches of 1000 calls, run after pytest; excludes cold
geography, database, network, observation rendering and live load. This is local CPU
evidence, not a live player-capacity claim.

## Baseline release context (historical)

**No DEV deployment. No PROD deployment. No cc-update invocation.** Active live runtime
revision was not checked. Thomas reviews the implementation report before manually
running the DEV release process, migrations and live browser/PostgreSQL verification.
Documentation publication is separate from game deployment. PROD is not authorized.

The next bounded verification is the manual DEV release and live testing of settings,
manual entry, automatic transitions, current drift, STOP/exhaustion and reconnect in
already certified HOG-generated channels. Unknown terrain stays inaccessible.
Lakes/coasts/oceans, absolute water-surface/riverbed Z, drowning, knockdowns, swimming
skills, new equipment penalties, rescue, boats and hydraulic realism remain deferred.
Current never displaces WADING characters.

Earlier investigation and release evidence remain accessible through [Evidence](evidence.html).
