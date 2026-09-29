---
title: Current status
description: Cormac Eyes observes surrounding HOG geography without walking materialization; deployment remains manual.
reviewed: 2026-09-29
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Cormac Eyes v1 adds server-side geographic observation and interpretation to wilderness
LOOK on private baseline `dde15adfbd346c3857570e62c3809925d7dec8a2`.
It reuses the active HOG provider for continuous elevation, regional biome/tree cover,
and one bounded discovery-catalog pass. Precise HERE water remains authoritative.

Meaningful rise/fall, broader biome changes and independently established cover changes
join landmarks in the existing clockwise paragraph. Percentages are replaced with simple
coverage language. Nearby and live Position remain outside geographic caching. Authored
interiors, RAW, session verbosity, login LOOK and movement retain their existing paths.

The directional survey requires no walking materialization. LOOK no longer prewarms its
surrounding walking neighborhood; movement prewarming is unchanged. Cold discovery is
asynchronous. No world generation, persistence, certificate or saved-position change is
introduced. See the [Eyes contract](hog.html#cormac-eyes-v1-2026-09-29) for source scales,
thresholds, fact limits, diagnostics and perception assumptions.

## Verification and next action

Implementation pushed: `d1d75c21eb142e514d06388f6da122b48847c2e7`.
Focused Eyes/Cormac/cabin tests: **86 passed**, one dependency warning, **62.04s**.
Complete suite: **635 passed**, 13 dependency deprecation warnings, **550.07s**.
Changed-file Ruff and whitespace checks pass. Repository-wide Ruff retains 55 existing
findings in untouched files. Shipped client CLEAR isolation and travel/water rendering
checks pass. No client implementation changed.

Real-seed examples cover all three verbosity modes at Origin, the known regional biome
transition, two streams, riffles/river and meaningful slope. Each LOOK generated zero
walking chunks. Origin remains locally nearly level; surrounding mixed forest and real
waterways now add context. No synthetic map or live world changes were used.

Local Windows/SQLite benchmarks, measured alongside regression work: directional
observation median **5.49 ms**, feature selection **26.83 ms**, warm full LOOK **34.21 ms**
(p95 **40.61 ms**). One cold LOOK returned in **16.46 ms** while background catalog work
still needed **2.51 seconds**. These are illustrative component/local command results,
not DEV network/PostgreSQL guarantees; a less-contended initial run measured warm LOOK
at 20.74 ms. The committed benchmark script reproduces the isolated workflow.

Thomas will manually deploy the reviewed revision and evaluate the real examples in DEV;
no live DEV browser verification is claimed.

## Limits and preserved behavior

Regional vegetation is coarse context, not a local forest-edge survey. Certified LOS,
weather/daylight filtering, fine vegetation, generic Nearby entities and authored outdoor
zones remain future work. The optional vegetation-detail raster is deferred. Sound is
an allowed future independent perception channel, but no authoritative hearing feed is
connected and no sound is inferred from geographic existence.

No DEV or PROD deployment, cc-update invocation or live migration occurs in this phase.
Water Movement v1, odometers, cabin interactions, continuous travel, terrain factors,
stamina and offline recovery remain unchanged. Walking-generation version 5 and HOG's
underlying seed behavior remain intact. No travel-prose overhaul or Origin hot zone.
