---
title: HOG investigation evidence
description: Durable reports and reproducible evidence for the completed Step 10 efficiency investigation.
reviewed: 2026-09-27
nav: hog
permalink: /CCMud/evidence.html
---

## Completed Step 10 efficiency investigation

The two complete reports and all delivered benchmark artifacts are preserved in
**private Crown-Call**, not in a transient chat folder. These links require access
to that repository. They are pinned to evidence commit
`e396c089d04ecbe29846c0ba0024452be5acb474` so future documentation changes do not
alter the measurements being cited.

| Evidence | Durable location |
| --- | --- |
| Full HOG Step 10 efficiency investigation | [HOG-efficiency-investigation.md](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/HOG-efficiency-investigation.md) |
| Full fixed-500-foot terrain sampling study | [terrain-spacing-study.md](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/terrain-spacing-study.md) |
| All reports, measurements and scripts | [Evidence directory](https://github.com/twpaige/Crown-Call/tree/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency) |
| File provenance and portable hashes | [MANIFEST.json](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/MANIFEST.json) |
| Chunk benchmark result JSON | [chunk-results.json](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/chunk-results.json) |
| Sample-spacing result JSON | [spacing-results.json](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/spacing-results.json) |
| Targeted real-edge audit and synthetic cases | [spacing-edge-audit.json](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/spacing-edge-audit.json) |
| Chunk / spacing summaries | [benchmark-summary.md](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/benchmark-summary.md), [spacing-summary.json](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/spacing-summary.json) |
| Reproduction and verification | [REPRODUCE.md](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/REPRODUCE.md), [verification.md](https://github.com/twpaige/Crown-Call/blob/e396c089d04ecbe29846c0ba0024452be5acb474/docs/evidence/hog-step10-efficiency/verification.md) |

Base implementation: `e54acc982c1b68450b85e9c428fd0e82daa64d9c`.
Principal seed: `867359018957601`. Measurements are local Windows/Python results;
capacity models and supplied large-area density context are labeled separately.
Reproduction scripts remain private documentation evidence and run only explicitly.
No benchmark was repeated to preserve this evidence.

The original delivered files, including the evidence ZIP and diagnostic patch,
are preserved. The patch is earlier uncommitted local work, not a deployed feature
or an application change made by this documentation commit. See the directory
README before considering it. The portable manifest records delivered-byte and
LF-normalized hashes for Windows/Git portability; original workstation paths in
historical reports are provenance, not required future paths.

## How future work should use this evidence

Read the maintained [HOG architecture](hog.html#step-10-efficiency-decisions-and-evidence-2026-09-27),
[design](design.html#world-time-travel-and-seasonal-light-2026-09-27) and
[current status](status.html) first. They distinguish implemented behavior, settled
unimplemented design, measurements and optional future architecture. Reports retain
detail and limitations; they do not authorize broad implementation or game deployment.
Major architecture changes may justify new measurements, but do not repeat the
completed study just to recover conclusions already recorded here.

## Bounded implementation release

The subsequent [implementation report](https://github.com/twpaige/Crown-Call/blob/0472cce96f168e775616ab3b39c0d121c0fa1ec6/docs/hog-efficiency-release.md) and [release measurements/scripts](https://github.com/twpaige/Crown-Call/tree/0472cce96f168e775616ab3b39c0d121c0fa1ec6/docs/evidence/hog-efficiency-release)
are preserved separately at code revision `0472cce96f168e775616ab3b39c0d121c0fa1ec6`. The original investigations
above remain unchanged. The release implements only immutable regional reuse,
reduced discovery lock contention, ordered prewarm reconciliation and the authorized
diagnostics; it does not deploy the game or adopt coarse sampling/time changes.

## Ravine Reservation Contract v1

[Release report](https://github.com/twpaige/Crown-Call/blob/2aabec9dd523a0e3a8efd325c76ca0a8525ce7df/docs/ravine-reservation-v1-release.md) and [durable evidence](https://github.com/twpaige/Crown-Call/tree/2aabec9dd523a0e3a8efd325c76ca0a8525ce7df/docs/evidence/ravine-reservation-v1) at private revision
`2aabec9dd523a0e3a8efd325c76ca0a8525ce7df` preserve the original refusal records, full proposal/methodology,
572-ravine sample across 300 seed/cell pairs, and separate implementation
measurements/tests. Private repository access is required. The study was not
repeated; approved conclusions became the versioned contract in HOG architecture.
The original Step 10 efficiency and terrain-sampling reports remain unchanged.

## Wetland Footprint v1

[Release report](https://github.com/twpaige/Crown-Call/blob/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/wetland-footprint-v1-release.md) and [evidence](https://github.com/twpaige/Crown-Call/tree/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/evidence/wetland-footprint-v1) at private code revision
`3812799921c4fa5ecfaef070fd82fcaf36d861d9` preserve the full wetland investigation, 2,554 generated records,
live source capture and diagnostic sensitivity results, plus separate simple-ellipse
measurements, reproduction driver and final regression/concurrency logs. Private
repository access is required. The investigation's hydrology-guided recommendation
is superseded by Thomas's explicit traversable ellipse decision, maintained in
[HOG](hog.html#wetland-footprint-v1-2026-09-27). No large calibration study was repeated.
