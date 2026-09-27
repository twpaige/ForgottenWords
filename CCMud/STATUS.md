---
title: Current status
description: Ravine Reservation Contract v1 implementation and release boundary.
reviewed: 2026-09-27
nav: status
permalink: /CCMud/status.html
---

## Current bounded release

**Ravine Reservation Contract v1 and safe exterior certification are implemented
and locally tested**, at private code revision `2aabec9dd523a0e3a8efd325c76ca0a8525ce7df`. The subsequent
pinned-documentation synchronization commit is the final release candidate.
Read the [contract](hog.html#ravine-reservation-contract-v1-2026-09-27),
[report](https://github.com/twpaige/Crown-Call/blob/2aabec9dd523a0e3a8efd325c76ca0a8525ce7df/docs/ravine-reservation-v1-release.md) and [evidence](https://github.com/twpaige/Crown-Call/tree/2aabec9dd523a0e3a8efd325c76ca0a8525ce7df/docs/evidence/ravine-reservation-v1).

The three live-case chunks [11560,68], [11560,69], [11559,69] now generate
certified qualifying exterior plus blocked ravine reservation. Full-segment
collision, expanded lookup, GROUND refusal, RAW and APPROACH-to-boundary are
locally verified. No interior geometry or traversal is implemented. Other natural
features retain their whole-chunk refusals. Water behavior is preserved.

Identity includes dev_walking=4 and ravine_reservation=1. Old character identity
compatibility requires current-coordinate certification. Inside a new reservation,
GROUND/standing is unavailable and placement audits require review; no persistent
object or character is silently relocated. Existing ABSOLUTE placement semantics
and structure blockers remain. Pioneer Cabin compatibility is still unaudited.

Full suite: **309 passed, 13 warnings in 411.55s (0:06:51)**. Repeated fresh-process concurrency/session checks:
**12 passed, 1 warning in 55.29s; 12 passed, 1 warning in 56.23s; 12 passed, 1 warning in 54.71s**. Focused ravine/water/regional suite: 26 passed.
Changed-file lint and diff checks pass; 55 pre-existing repository lint findings
remain. The report separates local benchmark cost from live performance claims.

## Environment and deployment evidence

Thomas reports live DEV ravine testing after the prior efficiency release. This
task did not inspect live revision/service/health, access a live database, or
deploy DEV/PROD. **DEV was not automatically deployed; PROD was untouched.**
The documented older Linux checks remain historical, not verification of this code.

## Recommended bounded next work

Thomas may manually use `sudo cc-update dev <exact-final-commit>` from the completion
report. Fresh Linux pytest, migrations and health/activation gates remain mandatory.
After successful activation, test:

```text
approach 867359018957601:C:natural:v1:site:7502:0:1
```

Expected: travel over certified exterior, stop immediately outside the reservation,
truthful ravine hazard, RAW perimeter diagnostics, no unexpected generator errors.
Also test toward/alongside/away/around movement and a through-crossing where practical.
Do not bypass an independent geography refusal or a failed deployment gate.

## Preserved work and deferred scope

Immutable regional reuse, ordered prewarm reconciliation, single-worker ownership,
session/thread safeguards and last-32 failure diagnostics remain. Cumulative failures
can count repeated geography refusals; inspect record category/message separately
from unexpected errors and queue rejection. Historical 13 failures remain unknown.

Retain 500-foot chunks /25-foot samples and current movement/stamina behavior.
Separate 1.75× world time, 2× convenience, dynamic 0.25×–2× travel control and seasonal
light remain approved future design, not runtime changes here; runtime still uses
its approximately 24× combined travel factor. Terrain representation/certification
decoupling, LOS/perception, prose, speculative corridors and remote workers remain
separate work. No additional feature-class traversal is part of this release.

The two original Step 10 studies and new ravine study are preserved in the
[evidence index](evidence.html). Complete this bounded release's DEV field acceptance
before tackling the next natural-feature problem. Documentation publication does
not deploy the game or authorize PROD.
