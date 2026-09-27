---
title: Current status
description: Implemented behavior, completed HOG investigations and bounded next work.
reviewed: 2026-09-27
nav: status
permalink: /CCMud/status.html
---

## Current milestone and environment evidence

**The Step 10 efficiency investigation and fixed-500-foot terrain-sampling
addendum are complete and durably preserved.** This update is documentation/evidence
only: no new benchmark, optimization, game-code release or DEV/PROD deployment.
Read [HOG decisions](hog.html#step-10-efficiency-decisions-and-evidence-2026-09-27),
the [design record](design.html#world-time-travel-and-seasonal-light-2026-09-27)
and [full evidence index](evidence.html) before starting further Step 10 work.

The inspected investigation base was private Crown-Call
`e54acc982c1b68450b85e9c428fd0e82daa64d9c`, seed `867359018957601`.
Thomas reports this as the DEV base and reported successful field behavior;
this supersedes the prior status's assumption that the field utilities awaited
deployment. This documentation task did not independently inspect the live symlink,
health or services. Earlier Linux verification at `48bdb3faa660` (225 tests,
migrations/health and exact release path) remains historical evidence, not proof
of the current live revision. PROD was not inspected or modified.

## Current implemented behavior

At the inspected base: 500×500-foot runtime chunks, 25-foot terrain samples with
coupled local certification, one bounded background generation worker, conservative
water/terrain blocking and precise continuous movement. Field utilities include
CLEAR, exact-heading TRAVEL, diagnostic APPROACH, grounded admin JUMP, STOP and
speed/heading status. Stamina/pace, database/session/thread ownership and worker
ownership safeguards remain unchanged. See [HOG field commands](hog.html#bounded-field-testing-commands).

Travel currently uses a combined approximately 24× factor; separate world-time
and travel-convenience services are not implemented. No complete world calendar,
dynamic travel controller, observer-dependent distant LOS or prose generator is
claimed. Distant RAW remains diagnostic, not perceived geography.

The earlier authorized **bounded failure-observability patch is implemented and
tested locally, but remains uncommitted/undeployed application work**. The evidence
commit preserves its patch without applying it as a documentation change. It
retains the last 32 failures with identity/chunk/time/category/message details in
memory and admin RAW; restart loses the history, and queue rejection stays separate.
Fresh clones have evidence of the patch, not the new runtime behavior. Inspect and
handle that work separately before any code release.

The historical cumulative 13 generation failures remain unexplained because no
messages/logs were saved. The plateau while hundreds of later chunks succeeded
and queue rejection stayed zero argues against a persistent total failure only.
Do not attribute those failures to unrelated benchmark refusals.

## Completed benchmark findings

- Equal-area dry generation: 500/250/100-foot chunks took approximately
  16.1/42.4/207.4 seconds. Retain 500-foot chunks for the current architecture.
- Fixed 500-foot chunks with 25/50/100-foot samples: approximately
  0.656/0.422/0.322 seconds wall per accepted chunk. At 100 feet, CPU fell 35.6%
  and object size 86.8%; wall savings partly include fewer cooperative sleeps.
- Accepted-terrain elevation differences stayed below 0.001 inch at 100 feet;
  four movement outcomes and water geometry agreed. All main-site and targeted
  real-edge certificate comparisons agreed, but synthetic tests expose reduced
  safety-probe coverage. Retain 25-foot production samples pending independent
  certification investigation.
- Repeated regional query/reconstruction/copy work and shared-lock contention
  are concrete optimization targets. Ordered prewarm-plan comparison can remove
  redundant APPROACH submissions while retaining retries and continuous steering.
- Speculative corridors are not the first optimization for a non-preemptive
  single-worker FIFO. Slower travel removed preparation delay in the tested model
  without speculation; this is not a live capacity guarantee.

These are local Windows measurements and explicitly labeled models, not new
Linux/PostgreSQL/multiplayer guarantees. The [evidence](evidence.html) preserves
117 spacing generation attempts, 64 targeted real-edge checks, original equal-area
chunk results, profiles, summaries, limitations and reproduction scripts. No
completed benchmark was rerun for this documentation task.

## Settled design not yet implemented

The [September 27 design continuation](design.html#world-time-travel-and-seasonal-light-2026-09-27)
sets world time to 1.75×, normal travel convenience to 2×, future dynamic convenience
to 0.25×–2×, and smooth seasonal daylight targets. Normal effective travel becomes
3.5×, implying 85.42% less steady virgin-terrain distance demand under comparable
conditions. Pressure affects geographic travel convenience, not world time or
ordinary gameplay. Numerical controller tuning remains future work.

## Recommended bounded next work

Implementation requires separate approval; do not combine these into one release.

1. Retain 500×500-foot chunks and 25-foot production sampling.
2. Optimize repeated regional certification/source queries without changing
   geography or weakening safety; preserve immutable/copy-isolated data and
   movement priority.
3. Suppress redundant APPROACH/prewarm submissions with ordered deduplicated plans
   and bounded reconciliation for rejection, failure, expiry and eviction.
4. In a separate release, introduce explicit 1.75× world time and 2× normal travel
   convenience, resolving calendar epoch/continuity and telemetry units.
5. Add dynamic 0.25×–2× travel throttling only after pressure telemetry, hysteresis
   and recovery behavior are tested. Do not throttle merely by player count.
6. Separately investigate candidate 100-foot terrain storage/interpolation with
   sufficiently fine independent certification and coarse-interpolant validation.
7. Later implement query-specific perception candidates, relevance horizons and
   on-demand LOS. Preserve precise movement hazards and detailed admin RAW.
8. Keep prose deferred. Distributed/home workers, full runtime pregeneration and
   speculative APPROACH corridors are future options, not immediate requirements.

Keep the diagnostic patch's review/release separate from these broader proposals.
The Pioneer Cabin remains unaudited; unknown water/crossing consequences remain
blocked. Preserve continuous travel, precise headings, APPROACH, JUMP, STOP,
stamina/pace, certified GROUND, responsive background work and database/thread safety.

## Verification and release boundaries

Original field utility verification at `84b8461` included 285 local tests and
three fresh-process concurrency runs of 12 tests each. The investigation's narrow
diagnostic verification and benchmark limitations are retained in its verification
report. These checks were not rerun as part of documentation publication.

Documentation verification checks evidence hashes, links, source scope, Pages
publication and pinned-reference integrity. Source publication and private reference
synchronization do not deploy the game. No DEV or PROD runtime modification is
authorized by this update; any future release needs its own exact revision and
environment verification.
