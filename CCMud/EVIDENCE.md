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
